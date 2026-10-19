<h1> My First Active Directory Home Lab </h1>

<h2> About this Project  </h2>
In this project, I will be creating an Active Directory Home Lab using Virtual Box. I will create 2 VM’s: One for a Domain Controller on a Windows Server 2019 which will be hosting Active Directory, and the other for a Windows 10 Client machine.

The Domain Controller will have 2 Virtual NICs configured for it: One for accessing the internet, and the other for connecting to the Virtual Box’s Private Network to which the clients will be connecting to (it will serve as the Default Gateway for the clients). Afterwards I will be installing ADDS and creating the domain. Then Routing and NAT will be configured in order for clients on the Private Network to be able to access the internet via the Domain Controller. Afterwards, I will be setting up a DHCP service on the Domain Controller so that the Client Machine can be given an IP Addess.

After the Domain Controller has been set up, I will write and execute a PowerShell Script that will populate Active Directory with 1000 users created from a list of fictitious names and surnames.

Finally, I will be creating a Client VM with Windows 10 installed, which will then be connected to the private Virtual Box Network.

Credit to Josh Madakor for his tutorial on the subject!

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-00.png" width=80% height=80%>

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

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-30.png" width=60% height=60%>

From the Server Manager, I will click on "Add roles and features" again, "Next", then I will confirm that "Role-based or feature-based installation" has been checked and click "Next":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-31.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-32.png" width=60% height=60%>

Now I will make sure that the correct server is selected and click "Next" again:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-33.png" width=60% height=60%>

Now, under Roles I will check "Remote Access" and click "Next":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-34.png" width=60% height=60%>

Then, in Role Services I will check the "Routing" option, which will automatically also select "DirectAcces and VPN (RAS), and click "Next" until I get to the Role Services window:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-35.png" width=60% height=60%>

Now I will click "Install" and after the installation has been completed, I will click on "Close"

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-36.png" width=60% height=60%>

From the Server Manager, I will now click on the Tools menu at the top right and navigate to "Routing and Remote Access":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-37.png" width=60% height=60%>

From here I will right-click on the Domain Controller (starting with "WIN") and click on "Configure and Enable Routing and Remote Access":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-38.png" width=60% height=60%>

Then I will click "Next" and select the "Network Address Translation (NAT)" option and click "Next":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-39.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-40.png" width=60% height=60%>

From here I will select the "Use this public interface to connect to the internet" option, and select the "INTERNET" NIC and click "Next" and "Finish":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-41.png" width=60% height=60%>

Now I will close the window and go back to the Server Manager.

<h2> Phase 4: DHCP Setup </h2>

I will now set up a DHCP server on the Domain Controller to allow the Windows 10 Clients to obtain an IP address to access the internet. From the Server Manager, I will again click on "Add roles and features", followed by "Next", followed by "Next" assuming the "Role-based or feature-based installation" option has been selected:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-42.png" width=60% height=60%>

From Server Selection, ensure that the correct (and only) Server is selected (starting with "WIN") and click "next":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-43.png" width=60% height=60%>

From the Server Roles window I will now check DHCP and click "Add Features":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-44.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-45.png" width=60% height=60%>

Now I will click "Next" until I get to Confirmation, at which point I will click "Install", and after the installation has completed I will close the window:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-46.png" width=60% height=60%>

I will now configure the DHCP's scope of available IP addresses to assign to the clients. Firstly, I will click on the Tools menu at the top right of the Server Manager and click on DHCP:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-47.png" width=60% height=60%>

From here I will click on the DHCP server, right-click on IPv4, and click on "New Scope":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-48.png" width=60% height=60%>

Then I will click "Next" and name the scope "172.16.1.100-200" (it was supposed to be "172.16.0.100-200" but the name is arbitrary), and click on "Next" again:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-49.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-50.png" width=60% height=60%>

Now I will set the Range: Start IP Address will be set to "172.16.0.100", End IP Address will be set to "172.16.0.200", the length will be set to "24", which will yield the Subnet Mask of "255.255.255.0":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-51.png" width=60% height=60%>

I do not need any exclusive IP addresses for this use case, so I will not assign any exclusions:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-52.png" width=60% height=60%>

I also do not have a need for lease periods longer or shorter than 8 days, so I will leave them as is:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-53.png" width=60% height=60%>

From the "Configure DHCP options" window, will will check "Yes, I want to configure these options now" and click "Next":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-54.png" width=60% height=60%>

From here we will add an IP address for a router which the clients will use to access the internet. We will use the IP address for the internal NIC (172.16.0.1) for the router IP and click "Add" and then "Next":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-55.png" width=60% height=60%>

Now I have to enter an IP address for a DNS server. When Active Directory Domain Services is installed, DNS is automatically added as well. Thus I will use the Domain Controller as the DNS server. I will enter the Domain Controller's IP address, click "Add", and then "next":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-56.png" width=60% height=60%>

Now I will click "Next", check "Yes, I want to activate this scope now", click "Next" again, and finally "Finish":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-57.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-58.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-59.png" width=60% height=60%>

Now to verify that everything is working, I will right-click on the DHCP server, click on "Autherize" and then again on "Refresh":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-60.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-61.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-62.png" width=60% height=60%>

<h2> Phase 5: Populating Active Directory with Users using a PowerShell Script </h2>
Now it is finally time to create some users with a PowerShell script. To do this, I will click on Start, then Windows PowerShell, followed by right-clicking Windows PowerShell ISE, selecting "More" and then "Run as Administrator":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-63.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-64.png" width=60% height=60%>

This is where I will write and execute the script. But before I do that, I will download a .txt list of over 1000 randomized names and surnames (courtesy of Josh Madakor) and save it to the desktop. At the top of this list I will also make a name entry for myself, named David Gouws, and put it at the very top of the list before saving and closing:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-65.png" width=60% height=60%>

Now for the script:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-66.png" width=60% height=60%>

First, I created 2 variables: On line 1, I created a variable named "$PASSWORD_FOR_USERS" and set it equal to "Password1". This created the password which all the users will use.
Then on Line 2, I created a variable named "$USER_NAMES_LIST" and set it equal to "Get-Content C:\Users\a-dgouws\Desktop\names.txt". What this does is it creates an array and populates it with the content of the names list that we downloaded earlier.

In Line 4, I created an object named "$password". I set it equal to "ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force", which took the variable "$PASSWORD_FOR_USERS" which I created earlier, and turns it into an object that PowerShell can use as a secure password. I will use this object later in a loop.

In line 5, I used the code "New-ADOrganitionalUnit -Name _USERS" to create a new Organizational Unit in Active Directory (similar to the _ADMINISTRATORS OU I created earlier in the project), and name it "_USERS".

Now I will break down the loop which spans lines 6 through 21:
On line 6, I created the loop with "foreach", followed by the parameters "($n in $USER_NAMES_LIST)" - This essentially specifies what the loop will be applied to, namely each name ($n) in the variable "$USER_NAMES_LIST" that we created earlier. I then type a "{" to mark the start of the loop specifications (that will be applied to each name).
On line 7-9, I specify new variables, namely "$first", "$surname" and "$username":
"$first" will be the first name, and on line 7 I set it equal to "$n.Split(" ")[0].ToLower()" - the $n specifies the whole name (name and surname). The ".Split(" ")" that follows, specifies where the string will be split (namely the space between the name and surname), the "[0]" specifies which element (0 is the first element) will be selected to be the variable "$first", and ".ToLower()" states that the entire element will be converted to lowercase.
"$surname" will be the surname, and it is almost identical to the code of "$first", with the exception of "[0]" being changed to "[1]", because we are using the second element in the string as the surname.
"$username" will be a concatenation of the 2 variables that I defined in the 2 lines of code above it, namely "$first" and "$surname". However I will only be using the first initial of the first name, so I will have to define a substring for that. I will do this by setting "$username" equal to ""$($first.Substring(0,1))$($surname)"" - this states that a substring from the first to second position (i.e the first character) of the variable "$first" will be taken, and concatenated to the entire "$surname" variable. Lastly the ".ToLower()" part states that the entire element will be converted to lowercase.

Line 10 is merely informational output - it is only there to let me know that a new user is being created. The "Write-Host" specifies that something is going to be outputted in text to the console, and the ""Creating user: $($username)"" part specifies what that something is. The "-BackgroundColor Black" and "-ForegroundColor Cyan" are aesthetic choices and self-explanatory.

Now that all of the variables have been defined, for the next part of the loop, I will populate the user information which will be used when the user will be created in Active Directory. I will define what will be used for the Account Password, the Given Name, the Surname, the Username/Display Name, the Employee ID, the path of the directory in which the user will be created, and also whether the password never expires and if the account should be enabled.
On line 12 I start the user creation process with "New-AdUser", followed by definitions for all the required fields.
Finally I conclude the loop with "}" to mark the end of the user creation process for that user.

One final step to take is to set the Execution Policy to Unrestricted, otherwise the script will not run. To do this, I will run the command "Set-ExecutionPolicy Unrestricted" and click "Yes to all" when prompted:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-67.png" width=60% height=60%>

Finally, I will run the script and create all of the users. After the script has finished, I will return to Active Directory Users and Computers, right-click the Domain and click "Refresh". Then I will right-click on the domain again and click "Find":

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-68.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-69.png" width=60% height=60%>
<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-70.png" width=60% height=60%>

From here I will click on "Find Now" while having the field blank. This should yield all of the created users which should total over 1000:

<img src="https://github.com/Dash-Colette/ActiveDirectoryLab/blob/main/IMG-71.png" width=60% height=60%>

<h1> Phase 6: Connecting the Wondows 10 Client to the Domain </h1>
