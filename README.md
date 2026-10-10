<h1> My First Active Directory Home Lab </h1>

<h2> About this Project  </h2>
In this project, I will be creating an Active Directory Home Lab using Virtual Box. I will create 2 VM’s: One for a Domain Controller on a Windows Server 2019 which will be hosting Active Directory, and the other for a Windows 10 Client machine.

The Domain Controller will have 2 Virtual NICs configured for it: One for accessing the internet, and the other for connecting to the Virtual Box’s Private Network to which the clients will be connecting to (it will serve as the Default Gateway for the clients). Afterwards I will be installing ADDS and creating the domain. Then Routing and NAT will be configured in order for clients on the Private Network to be able to access the internet via the Domain Controller. Afterwards, I will be setting up a DHCP service on the Domain Controller so that the Client Machine can be given an IP Addess.

After the Domain Controller has been set up, I will write and execute a PowerShell Script that will populate Active Directory with 1000 users created from a list of fictitious names and surnames.

Finally, I will be creating a Client VM with Windows 10 installed, which will then be connected to the private Virtual Box Network.

Credit to Josh Madakor for his tutorial on the subject!

<h2> Phase 0: Setting Up Virtual Box and Installing Windows Server 2019 </h2>

<h2> Phase 1: Setting up the Domain Controller's Network Adapters </h2>
Now that the Windows Server Environment has been set up, I will configure the Network Adapter options in order to set up the Virtual NICs. The Internet NIC will automatically be assigned an IP address from the local router, so no action is required here. The NIC for the Internal Network however, will need to be configured manually. To do this, I will first click on the Network Icon on the bottom right corner of the screen, and then I will click on Network:

![Text](https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-01.png)

This opened the Network and Internet Settings interface. From here, I will click on Change Adapter Settings and it will take me to a screen where I can see both of the Virtual NICs:

![IMG-02](https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-02.png)
![IMG-03](https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-03.png)

Now I have to identify the Private Network's NIC. To do that, I will first Right-Click on one of the NICs, click on Status, followed by clicking on Details. This will show me, among other things, the IPv4 address of the NIC. Now, we are looking to see which NIC has an APIPA address (169.254.x.x) - This will be the Private Network NIC:

![IMG-04](https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-04.png)
![IMG-05](https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-05.png)
![IMG-06](https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-06.png)

For clarity's sake, I will rename both of the NICs accordingly: I will rename the Private Network NIC to "_LOCAL_INTRANET_" and the other I will name "_INTERNET_":

![IMG-07](https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-07.png)

Next, I will assign the Private Network NIC a static IP address of 172.16.0.1. To begin I will Right-Click on "_LOCAL_INTRANET_" and click on Properties, then Double-Click on IPv4:

![IMG-08](https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-08.png)

From here, I will click on "Use the following IP address" and then assign the NIC and IP of 172.16.0.1 and a Subnet Mask of 255.255.255.0. Notice that I did not assign a Default Gateway, as the Internal NIC IS the Default Gateway for all of the Client Devices. I will also enter the preferred DNS server, which will be the device itself, so I will enter the Loopback address of 127.0.0.1:

![IMG-09](https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-09.png)

Lastly I will click OK, then OK again, and finally close the Properties window.

<h2> Phase 2: Setting up Active Directory </h2>
