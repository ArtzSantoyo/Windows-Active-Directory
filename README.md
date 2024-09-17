First I start with Creating a new VM with Windows Server 2019 inside Oracle VM VirtualBox Manager 7.0

In Network settings keep the Adaptar 1 As Nat, and enable Adaptar 2 as Internal Network

At the installation I Select one of the Desktop Experience OS

After the installation is complete then I go to Virtualbox options / devices / and select " Insert guess addition CD image", then install Oracle VM Virtualbox Guest additions 7.0.18 and reboot the VM

Then right click the windows logo /system/rename this pc (To "DC-19"); so I can Identify my DC Server easily and restart again the VM ** Rename

When the Reboot is completed, then I go to Network Connections, Check for both Ethernet networks and verify wich one is connected to the internet (the one with IpV4 Address matching the VM) and wich one is looking for intranet connection (the one looking for a DHCP Server conection but currently unable to find one) and rename them as "internet" and "intranet" respectively and assing an IP to it

For Assigning an IP I go to Network connections / Select intranet and right click in properties/ option internet protocol version 4 (TCP/ IPV4) / Double-Click and in the options I choose "Use the following Ip Address" and I use the next Parameters: IP 172.16.0.1 SubMask 255.255.255.0 and for the DNS Server I could go for its own IP Address or the Loop IP address 127.0.0.1 since when the AD is installed it will automatically install the DNS

At Server Manager I go to: Configure this local server / add roles and features: there I select the server I want to install roles and features from the server pool/ and then in server roles I make sure to mark active directory domain services ** server

After that I have to Create the Domain Per se, I go to Deployment configuration and mark-up create a new forest that I named "santoyodomain.com", then going next throught the options until installation, afterwards the vm is going to reboot to apply the new settings and the domain can be seem from the start-up screen

I proceed to add AD users and computers in the Windows Tab and create a new organizational unit "Admins" and then a new user inside admins, next I select the user properties and go to member of/domain users and add it to groups of domains admins

Then I log out and start session with the new users credentials and go to server manager/ add roles and features/ server roles and mark-up "Remote Acces" and proceed until role services and select "routing", wait for the feature to install

Next from server manager/ tools / Routing and Remote Access I enable Routing on DC-19 marking the option of NAT (Network Address Translations) to allow internal clients to connect to the internet using one address, after that in the NAT options I go for/ use this public interfase to connect to the internet: and select the one I named "internet" from the network interfases

The next step is to add DHCP to allow users to connect to the internet even if they are using the internal network, Go to Domain controllers / Add roles and features/ Server roles / select DHCP features and proceed until installation, after that I go to server manager/tool/DHCP to set up the scope of IPs, at the DHCP panel I go to IPv4/ right click/ new scope: 172.16.0.100-200 that would be the range of our IPs with starting IP 172.16.0.100 and ending IP 172.16.0.200, without any exclusions and a lease duration of in this case 8 hours (it means how long a pc can have the same IP before it needs to refresh), and for default getaway Im going to add the server IP and then proceed until scope activation, after that select the DHCP host / right click/ authorize to put on-line the server

Afterwards I create and user account: Windows tab/ Active directory users and computers/users/right-click to create a new user wich in this case I called "pia" to test my AD

WIN 10

To Test my AD server, I create a new VM with Windows 10 that I will name "Cliente" using the same Oracle VM VirtualBox Manager, and in the network settings for adapter 1 I choose "intranet" that was configured previously, start running the VM and Log-in as the user "pia", then to confirm that the AD is properly working I Check in my Server VM the DCHP Leases and in my Windows 2010 VM the user and group
msantoyo / saori / SaoriSantoyo7
