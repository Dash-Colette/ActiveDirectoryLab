<h1> My First Active Directory Home Lab </h1>

<h2> About this Project  </h2>
In this project, I will be creating an Active Directory Home Lab using Virtual Box. I will create 2 VM’s: One for a Domain Controller on a Windows Server 2019 which will be hosting Active Directory, and the other for a Windows 10 Client machine.

The Domain Controller will have 2 Virtual NICs configured for it: One for accessing the internet, and the other for connecting to the Virtual Box’s Private Network to which the clients will be connecting to. Afterwards I will be installing ADDS and creating the domain. Then Routing and NAT will be configured in order for clients on the Private Network to be able to access the internet via the Domain Controller. Afterwards, I will be setting up a DHCP service on the Domain Controller so that the Client Machine can be given an IP Addess.

After the Domain Controller has been set up, I will write and execute a PowerShell Script that will populate Active Directory with 1000 users created from a list of fictitious names and surnames.

Finally, I will be creating a Client VM with Windows 10 installed, which will then be connected to the private Virtual Box Network.

Credit to Josh Madakor for his tutorial on the subject!
