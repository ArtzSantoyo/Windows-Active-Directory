Resources used: Oracle VM VirtualBox, Windows 2019 server ISO, Windows 10 ISO

The plan is to Create an AD environment using 2 Virtual Machines, one being the windows server 2019 managing the AD with a direct connection to internet and an internal net for the AD users of the domain, with the next structure:

![structure](https://github.com/user-attachments/assets/e6ade002-eb87-4997-9f8b-fa1d337c736d)


First I start with Creating a new VM with Windows Server 2019 inside Oracle VM VirtualBox Manager 7.0

![AD1](https://github.com/user-attachments/assets/5e4f8a17-b8e8-4d49-b7cd-3901132476ff)


In Network settings keep the Adapter 1 As Nat, and enable Adapter 2 as Internal Network

![ad2](https://github.com/user-attachments/assets/1c6af267-667d-4058-abbf-68a9e25c3142)


At the installation I Select one of the Desktop Experience OS

![ad3](https://github.com/user-attachments/assets/14629688-7fa8-4a26-a3d9-7e92e90564fd)

After the installation is complete then I go to Virtualbox options / devices / and select " Insert guess addition CD image", then install Oracle VM Virtualbox Guest additions 7.0.18 and reboot the VM

![ad4](https://github.com/user-attachments/assets/a529122f-d96b-4ca1-b601-9b49e63caba4)


Then right click the windows logo /system/rename this pc (To "DC-19"); so I can Identify my DC Server easily and restart again the VM

![rename](https://github.com/user-attachments/assets/1c1a4ef8-9297-4c79-97d5-ff1a84953184)

When the Reboot is completed, then I go to Network Connections, Check for both Ethernet networks and verify wich one is connected to the internet (the one with IpV4 Address matching the VM) and wich one is looking for intranet connection (the one looking for a DHCP Server conection but currently unable to find one) and rename them as "internet" and "intranet" respectively and assing an IP to it

![ad5](https://github.com/user-attachments/assets/d718d2dc-e5c5-44de-9700-d98fffc9eae9)


For Assigning an IP I go to Network connections / Select intranet and right click in properties/ option internet protocol version 4 (TCP/ IPV4) / Double-Click and in the options I choose "Use the following Ip Address" and I use the next Parameters: IP 172.16.0.1 SubMask 255.255.255.0 and for the DNS Server I could go for its own IP Address or the Loop IP address 127.0.0.1 since when the AD is installed it will automatically install the DNS

![ad6](https://github.com/user-attachments/assets/586c917c-79b4-4486-9114-c0fe26772395)

![ad7](https://github.com/user-attachments/assets/0cdfb0cd-4f67-49fa-965b-e4f808c404ea)




At Server Manager I go to: Configure this local server / add roles and features: there I select the server I want to install roles and features from the server pool/ and then in server roles I make sure to mark active directory domain services 

![ad8](https://github.com/user-attachments/assets/591c1a8b-f2fd-4743-98c8-bc8c8e47543b)

![server](https://github.com/user-attachments/assets/0d351bb0-8b80-4b61-9be9-c4b97fcdd0f5)



After that I have to Create the Domain Per se, I go to Deployment configuration and mark-up create a new forest that I named "santoyodomain.com", then going next throught the options until installation, afterwards the vm is going to reboot to apply the new settings and the domain can be seem from the start-up screen

![ad9](https://github.com/user-attachments/assets/001655e6-2ec2-4455-bf1c-1f78d85fff42)

I proceed to add AD users and computers in the Windows Tab and create a new organizational unit "Admins" and then a new user inside admins, next I select the user properties and go to member of/domain users and add it to groups of domains admins

![ad10](https://github.com/user-attachments/assets/32566011-df8c-4081-b467-5327228a6aef)

![ad12](https://github.com/user-attachments/assets/3b0e76b1-7680-4859-84d7-2423e4db8044)

Then I log out and start session with the new users credentials and go to server manager/ add roles and features/ server roles and mark-up "Remote Acces" and proceed until role services and select "routing", wait for the feature to install

![ad13](https://github.com/user-attachments/assets/96a70450-efea-4e0c-a7cd-85d4bc8353d9)


Next from server manager/ tools / Routing and Remote Access I enable Routing on DC-19 marking the option of NAT (Network Address Translations) to allow internal clients to connect to the internet using one address, after that in the NAT options I go for/ use this public interfase to connect to the internet: and select the one I named "intranet" from the network interfases

![ad14](https://github.com/user-attachments/assets/3ba14721-879c-415e-b74b-136d600bf2c3)


The next step is to add DHCP to allow users to connect to the internet even if they are using the internal network, Go to Domain controllers / Add roles and features/ Server roles / select DHCP features and proceed until installation, after that I go to server manager/tool/DHCP to set up the scope of IPs, at the DHCP panel I go to IPv4/ right click/ new scope: 172.16.0.100-200 that would be the range of our IPs with starting IP 172.16.0.100 and ending IP 172.16.0.200, without any exclusions and a lease duration of in this case 8 hours (it means how long a pc can have the same IP before it needs to refresh), and for default getaway Im going to add the server IP and then proceed until scope activation, after that select the DHCP host / right click/ authorize to put on-line the server

![ad15](https://github.com/user-attachments/assets/396459e1-f2dc-41dd-8c78-04635342ae2e)

![scope](https://github.com/user-attachments/assets/b2d984ad-3f70-4c1f-9100-c11185b9b8cb)

![ad16](https://github.com/user-attachments/assets/f1b23cb1-8b92-4793-a657-b65f283e5443)


Afterwards I create and user account: Windows tab/ Active directory users and computers/users/right-click to create a new user wich in this case I called "felipe" to test my AD

![users](https://github.com/user-attachments/assets/0ffd5bf7-be4c-4a4b-8089-eb741bdfec5c)


WIN 10

To Test my AD server, I create a new VM with Windows 10 that I will name "Cliente" using the same Oracle VM VirtualBox Manager, and in the network settings for adapter 1 I choose "intranet" that was configured previously, start running the VM and Log-in as the user "felipe", then to confirm that the AD is properly working I Check in my Server VM the DCHP Leases and in my Windows 2010 VM

![final](https://github.com/user-attachments/assets/af19268e-7e63-4104-9a16-d9689f0587ac)



