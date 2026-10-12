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

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-01.png" width=60% height=60%>

This opened the Network and Internet Settings interface. From here, I will click on Change Adapter Settings and it will take me to a screen where I can see both of the Virtual NICs:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-02.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-03.png" width=60% height=60%>

Now I have to identify the Private Network's NIC. To do that, I will first Right-Click on one of the NICs, click on Status, followed by clicking on Details. This will show me, among other things, the IPv4 address of the NIC. Now, we are looking to see which NIC has an APIPA address (169.254.x.x) - This will be the Private Network NIC:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-04.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-05.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-06.png" width=60% height=60%>

For clarity's sake, I will rename both of the NICs accordingly: I will rename the Private Network NIC to "_LOCAL_INTRANET_" and the other I will name "_INTERNET_":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-07.png" width=60% height=60%>

Next, I will assign the Private Network NIC a static IP address of 172.16.0.1. To begin I will Right-Click on "_LOCAL_INTRANET_" and click on Properties, then Double-Click on IPv4:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-08.png" width=60% height=60%>

From here, I will click on "Use the following IP address" and then assign the NIC and IP of 172.16.0.1 and a Subnet Mask of 255.255.255.0. Notice that I did not assign a Default Gateway, as the Internal NIC IS the Default Gateway for all of the Client Devices. I will also enter the preferred DNS server, which will be the device itself, so I will enter the Loopback address of 127.0.0.1:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-09.png" width=60% height=60%>

Lastly I will click OK, then OK again, and finally close the Properties window.

<h2> Phase 2: Setting up Active Directory </h2>
Now that the NICs have both been configured, I can move on to installing Active Directory Domain Services and create a Domain. To do this, I will first navigate to the Server Manager and click on "Add Roles and Features" followed by "Next":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-10.png" width=60% height=60%>

From here, I made sure that the "Role-based or Feature-based installation" option was checked and clicked "Next":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-11.png" width=60% height=60%>

From the next screen, there should only be one server on the list of available servers. I will ensure that it was selected and click "Next":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-12.png" width=60% height=60%>

From the Server Roles window, there will be a list of many available roles. I will be selecting Active Directory Domain Services, click on "Add Features" and finally click on "Next":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-13.png" width=60% height=60%>

Afterwards I will continue to click "Next" until I get to the Confirmation window, at which point I will click on "Install" and wait for the installation to complete and close the window:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-14.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-15.png" width=60% height=60%>

Now, Active Directory Domain Services has been installed, however the domain itself has not yet been created. So, a Post-deployment Configuration has to be done to create a domain. To do this, I will click on the Notifications icon on the top right of the Server Manager window and click on the "Promote this server to a domain controller" option:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-16.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-17.png" width=60% height=60%>

This will open the Deployment Configuration window. From here I will click on the "Add a new forest" option and name it "mydomain.com" (the name is arbitrary), and click "Next":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-18.png" width=60% height=60%>

The next window will be the Domain Controller Options window. I will not be using the DRSM feature, but I have to configure it with a password regardless because it is mandatory. I will simply use "Password1". Afterwards I will continue to click on "Next" until I get to the Prerequisites Check window and click on "Install". After the installation has been completed, the VM will automatically be restarted:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-19.png" width=60% height=60%>

Now for the next part I will be creating my own dedicated Domain Admin Account, which I will be using instead of the native administrator account. First I will log in to the Domain Controller, then I will open "Active Directory Users and Computers", which can be found under Start > Windows Administrative Tools:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-20.png" width=60% height=60%>

From here, I will right-click on "mydomain.com", navigate to "New" and then "Organizational Unit":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-21.png" width=60% height=60%>

I will name this OU "ADMINISTRATORS" and click "OK":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-22.png" width=60% height=60%>

Next, inside of the newly-created ADMINISTRATORS OU, I will create a new user account. I will right-click on the ADMINISTRATORS OU and navigate to "New" and then "User". Then I will input my personal details: my name, surname, and a User logon name of "a-dgouws". Afterwards I will set the password as "Password1", uncheck the "User must change password at next logon" option, and check the "Password never expires" option. Finally, from the next window, I will confirm all of the details and click "Finish":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-23.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-24.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-25.png" width=60% height=60%>

Now I will promote the newly-created account to a Domain Admin. I will right-click on the account and click on "Properties":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-26.png" width=60% height=60%>

From here, I will navigate to the "Member of" tab, click "Add", enter "Domain Admins" in the text field and click "Check Names" (this will resolve the text field into the actual Domain Admins), and finally click "OK" "Apply" and "OK":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-27.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-28.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-29.png" width=60% height=60%>

<h2> Phase 3: Installing RAS / NAT (Remote Access Server / Network Address Translation) </h2>
Now that I have set up Active Directory Domain Services and created an Administrator account, I will be installing RAS/NAT on the Domain Controller. What this is will do is enable all the local Windows 10 clients to both connect to the local private network, as well as to the internet via the 2 NICs on the Domain Controller which I configured earlier.

To begin, I will log into the Domain Controller on the new Administrator Account (a-dgouws) which I just created:

IMG-30

From the Server Manager, I will click on "Add roles and features" again, "Next", then I will confirm that "Role-based or feature-based installation" has been checked and click "Next":

IMG-31
IMG-32

Now I will make sure that the correct server is selected and click "Next" again:

IMG-33

Now, under Roles I will check "Remote Access" and click "Next":

IMG-34

Then, in Role Services I will check the "Routing" option, which will automatically also select "DirectAcces and VPN (RAS), and click "Next" until I get to the Role Services window:

IMG-35

Now I will click "Install" and after the installation has been completed, I will click on "Close"

IMG-36

From the Server Manager, I will now click on the Tools menu at the top right and navigate to "Routing and Remote Access":

IMG-37

From here I will right-click on the Domain Controller (starting with "WIN") and click on "Configure and Enable Routing and Remote Access":

IMG-38

Then I will click "Next" and select the "Network Address Translation (NAT)" option and click "Next":

IMG-39
IMG-40

From here I will select the "Use this public interface to connect to the internet" option, and select the "INTERNET" NIC and click "Next" and "Finish":

IMG-41

Now I will close the window and go back to the Server Manager.

I will now set up a DHCP server on the Domain Controller to allow the Windows 10 Clients to obtain an IP address to access the internet. From the Server Manager, I will again click on "Add roles and features", followed by "Next", followed by "Next" assuming the "Role-based or feature-based installation" option has been selected:

IMG-42

From Server Selection, ensure that the correct (and only) Server is selected (starting with "WIN") and click "next":

IMG-43

From the Server Roles window I will now check DHCP and click "Add Features":

IMG-44
IMG-45

Now I will click "Next" until I get to Confirmation, at which point I will click "Install", and after the installation has completed I will close the window:

IMG-46

I will now configure the DHCP's scope of available IP addresses to assign to the clients. Firstly, I will click on the Tools menu at the top right of the Server Manager and click on DHCP:

IMG-47

From here I will click on the DHCP server, right-click on IPv4, and click on "New Scope":

IMG-48

Then I will click "Next" and name the scope "172.16.1.100-200" (it was supposed to be "172.16.0.100-200" but the name is arbitrary), and click on "Next" again:

IMG-49
IMG-50

Now I will set the Range: Start IP Address will be set to "172.16.0.100", End IP Address will be set to "172.16.0.200", the length will be set to "24", which will yield the Subnet Mask of "255.255.255.0":

IMG-51

I do not need any exclusive IP addresses for this use case, so I will not assign any exclusions:

IMG-52

I also do not have a need for lease periods longer or shorter than 8 days, so I will leave them as is:

IMG-53

From the "Configure DHCP options" window, will will check "Yes, I want to configure these options now" and click "Next":

IMG-54

From here we will add an IP address for a router which the clients will use to access the internet. We will use the IP address for the internal NIC (172.16.0.1) for the router IP and click "Add" and then "Next":

IMG-55

Now I have to enter an IP address for a DNS server. When Active Directory Domain Services is installed, DNS is automatically added as well. Thus I will use the Domain Controller as the DNS server. I will enter the Domain Controller's IP address, click "Add", and then "next":

IMG-56

Now I will click "Next", check "Yes, I want to activate this scope now", click "Next" again, and finally "Finish":

IMG-57
IMG-58
IMG-59

Now to verify that everything is working, I will right-click on the DHCP server, click on "Autherize" and then again on "Refresh":

IMG-60
IMG-61
IMG-62
