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
