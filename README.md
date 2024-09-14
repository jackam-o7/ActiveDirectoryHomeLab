<h1>Active Directory Home Lab</h1>


<h2>Description</h2>
In the ever-evolving landscape of cybersecurity, hands-on experience with identity and access management systems like Active Directory Domain Services (AD DS) is invaluable. As part of my journey to build a comprehensive understanding of cybersecurity, I have embarked on a project to create a home lab environment that simulates a corporate network using Active Directory, setup using VirtualBox.
<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b> 
- <b>VirtualBox</b>
- <b>GitHub</b>

<h2>Environments Used </h2>

- <b>Windows 10 Pro</b> 
- <b>Windows 2019 Server</b> 

<h2>Program walk-through:</h2>
The network diagram illustrates the setup of a virtualized lab environment using VirtualBox, where the primary components include a Domain Controller (running on Windows Server 2019) and a client machine (running on Windows 10). The purpose of this setup is to emulate a corporate network environment, allowing for the testing and management of network services like Active Directory, DHCP, and DNS.
<br/> <br/><img src="https://i.imgur.com/MD1wZzw.png" height="40%" width="40%" alt="Network Diagram"/>


<p align="left">
To initiate my home lab environment, I began by downloading Windows Server 2019 and Windows 10 ISO files. The Windows 10 ISO file will be used later, for now I just need to create a new virtual machine on VirtualBox and mount the Windows Server 2019 ISO file to the VM's optical drive. 
 <br/>

After the installation of Windows Server 2019, I proceeded to configure and verify the network connections to ensure proper connectivity both internally and externally: <br/>  <br/>
<b> 1) Access VirtualBox Network Settings: </b>  <br/>
- Opened VirtualBox and selected the Windows Server 2019 VM.  <br/>
- Navigated to Settings and then to the Network tab.  <br/>

<b> 2) Configure Network Adapters: </b>  <br/>
- Adapter 1: Set to NAT (or Bridged Adapter), which provides the VM with internet access. <br/>
- Adapter 2: Configured as Host-Only Adapter or Internal Network for internal communication between VMs. <br/>

<b> 3) Verify Network Configuration in Windows Server 2019: </b> <br/>
- Started the VM and logged in to Windows Server 2019. <br/>
- Opened Control Panel and accessed Network and Sharing Center. <br/>
- Selected Change adapter settings to view and manage network adapters. <br/>

<b> 4) Check IP Configuration: </b> <br/>
- Right-clicked on each network adapter and selected Properties. 
- Renamed them ‘Internet’ and ‘Internal’ respectively. 
- Configured the Internal connections IPv4 properties so it connects to the Internal network 
<br/>

<img src="https://i.imgur.com/Dmuog8x.png" height="40%" width="40%" alt="IP Configuration"/> <br/>
<h3> Renaming the Server </h3> 
With the IP addresses properly configured on the Windows Server 2019 VM, the next step in setting up my home lab environment is to rename the machine to 'DC'. This renaming is a crucial part of the configuration process. The name 'DC' stands for "Domain Controller," reflecting the role this server will play within the network. <br/> <br/> In a typical network setup, a Domain Controller (DC) is responsible for managing and authenticating user access to network resources, enforcing security policies, and maintaining the directory services for the domain. By naming the server 'DC', I am laying the groundwork for a well-organized and efficient home lab environment. This step ensures that the server’s role is clearly defined and easily identifiable, which is crucial for both current configuration and future management.
<br/> <br/> 

<h3> Installing Active Directory Domain Services (AD DS) </h3> 
With the server renamed to 'DC' and network configurations in place, the next step is to install the Active Directory Domain Services (AD DS) feature on Windows Server 2019. This step is crucial for setting up the server as a Domain Controller and enabling domain management capabilities. Once this is successfully installed, I need to promote the server to a Domain Controller. This process involves configuring the server to manage and authenticate users within a domain. This setup allows it to handle domain authentication, manage user accounts, and enforce domain policies, completing the core setup for your home lab environment. </br> </br>
<img src="https://i.imgur.com/6r9hnaR.png" height="40%" width="40%" alt="AD DS successfully installed"/> 
After the server restarted following promotion to DC, we can see that the login screen displays MYDOMAIN/Administrator as the sign-in domain. MYDOMAIN represents the domain name which I configured during the AD DS setup, and administrator is the default administrative account to perform domain management tasks. Next, I wanted to create my own dedicated admin account on the Windows Server 2019 environment, this would ensure that administrative tasks are performed under a specific, manageable account rather than using the default Admin account. This helps in maintaining security and better tracking of administrative activities within the domain.


<img src="https://i.imgur.com/WYCgYDj.png" height="40%" width="40%" alt="Personal admin account"/> <img src="https://i.imgur.com/xTawUTb.png" height="40%" width="40%" alt="Personal admin account"/>  </br> </br> 

<h3> Installing RAS/NAT </h3>
My next step was installing RAS and NAT on the DC, this will enable the Windows 10 client (once created), to be part of the private virtual network whilst still having internet access through the domain controller. By implementing RAS and NAT, I can ensure that internal network traffic is routed appropriately and that the client can reach external resources, all while maintaining network security and isolation.


<img src="https://i.imgur.com/iFSheqL.png" height="80%" width="80%" alt="RAS and NAT"/>  </br> </br> 

<h3> Setting up DHCP Server</h3>

Next, I needed to set up a DHCP server on my DC. This would ensure that the Windows 10 clients on the private internal network receive IP addresses automatically, enabling those clients to communicate within the network and acces the internet through the Domain Controller, despite being isolated on a private network. Setting up DHCP streamlines network management by automating IP address assignment and ensuring that clients have the necessary network configurations to function correctly and browse the internet.

<img src="https://i.imgur.com/eSKRxyV.png" height="40%" width="40%" alt="DHCP installation"/> <br/>
I configured the DHCP server with a scope to automatically provide IP addresses to clients, set within the range 172.16.0.100-200. This means that any client requesting an IP address will receive one from this specified range. The subnet mask for this range is 255.255.255.0, which supports a subnet length of /24. This setup ensures that clients on the private network, including the Windows 10 machines, can obtain an IP address automatically and access the internet through the domain controller, maintaining efficient network management and connectivity.
<img src="https://i.imgur.com/JVQzkT6.png" height="60%" width="60%" alt="DHCP installed"/> <br/>

<h3>Populating the Server using PowerShell</h3>
The next step involves using a PowerShell script to populate the server with user accounts. This approach allows for efficient and automated creation of multiple user accounts, which is particularly useful in larger environments or when setting up a test lab.
By using PowerShell, I can automate the process of user account creation, reducing manual effort and minimizing the risk of errors. The script can be tailored to specify user attributes such as usernames, passwords, and organizational units, ensuring consistency across all accounts. This method streamlines the deployment of user accounts and facilitates easier management of the Active Directory environment. </br> </br>


<b>Enabling internet from Domain Controller</b> </bR>
Before populating the server with users, I need to configure the domain controller to allow browsing the internet. This step involves setting up the necessary configurations to enable internet access from the domain controller. While this approach is acceptable for a lab environment, it's important to note that allowing internet access on a domain controller is generally not recommended in production settings due to security concerns. In a production environment, best practices dictate keeping domain controllers isolated from direct internet access to minimize security risks. <br/>
<img src="https://i.imgur.com/pMz6tjq.png" height="40%" width="40%" alt="Enabling Internet from DC"/> <br/>

To populate the server with users, I downloaded a [PowerShell script](https://github.com/joshmadakor1/AD_PS) from a GitHub repository. The script automates the process of creating multiple user accounts in Active Directory. This script is particularly useful for efficiently setting up a lab environment with a large number of user accounts, allowing for consistent and error-free account creation. By leveraging this script, I can quickly and easily populate the domain with the necessary users, streamlining the overall setup process.

<img src="https://i.imgur.com/0iwgkXt.png" height="60%" width="60%" alt="Powershell script"/> <br/>

In order to run the script, I need to enable script execution on the server. To do so, I used the following Powershell command: </br> </br>
'Set -ExecutionPolicy Unrestricted' </br> </br>
By choosing Unrestricted, I allowed all PowerShell scripts to run without restrictions, regardless of their source. This means that any script, whether created locally or downloaded from the internet, can be executed without requiring a digital signature. While this setting is more permissive and convenient for a lab environment, it's important to be cautious, as it can pose security risks in a production environment. With the execution policy set to Unrestricted, I can now run the PowerShell script to populate the server with users. Running the script will automate the creation of multiple user accounts in Active Directory, streamlining the process and ensuring that the user accounts are created quickly and consistently. This step is crucial for setting up the lab environment, as it allows for the efficient deployment of the necessary users to test and manage various network and security scenarios.

<img src="https://i.imgur.com/f88I4FT.png" height="80%" width="80%" alt="Populated server"/> <br/>
Running the PowerShell script successfully populated the server with users, marking a significant milestone in the project. With most of the essential components now set up, including the domain controller, DHCP, RAS/NAT, and the user accounts, the foundation of the lab environment is firmly in place. This setup provides a robust platform for further exploration and testing, allowing for a comprehensive study of Active Directory and network management within a controlled environment. The next steps will likely involve refining configurations, testing different scenarios, and possibly adding more advanced features to enhance the lab’s functionality.

<h3>Client Integration: Adding Windows 10 to the Lab Environment</h3>
With most of the necessary components set up, the final step is to add the Windows 10 client to the lab environment. Windows 10 will serve as the client system, interacting with the domain controller and other network services. Once the Windows 10 client is added and properly configured, the lab environment will be fully operational, allowing for comprehensive testing and exploration of how clients interact within the domain, including user authentication, network access, and security policies. To begin integrating the Windows 10 client into the lab environment, I'm configuring its network settings to be attached to the internal network, rather than using the NAT configuration that was applied to the Windows Server 2019 setup. This setup emulates a corporate network, ensuring that the Windows 10 client operates within the same private network as the domain controller. By doing so, the client can properly interact with network services such as DHCP and Active Directory, closely mirroring how client devices function in an actual enterprise environment.
<img src="https://i.imgur.com/hKFC2Kr.png" height="60%" width="60%" alt="Windows 10 Configuration"/> 
With the network settings configured, its time to install Windows 10 Pro on the client machine. I opted for Windows 10 Pro rather than Windows 10 Home because the Home edition is unable to join a domain, which is essential to my home lab setup. Windows 10 Pro allows for full client integration with the DC, enabling the full range of testing and management functionalities required for this project. </br>
<img src="https://i.imgur.com/Y3l0Jae.png" height="40%" width="40%" alt="Windows 10 Installation"/> 
Now that the Windows 10 VM is set up for client use, I ran ipconfig in Command Prompt to check the network configuration. While the VM has been assigned an IP address, I noticed that there is no default gateway listed. This indicates that the Windows 10 VM is not currently connected to the internet, which is a crucial step for ensuring full network functionality and client access to external resources. I'll need to address this issue to ensure proper network connectivity for the VM.
<img src="https://i.imgur.com/6FGlYB6.png" height="40%" width="40%" alt="No Gateway"/> 

</br> <b>Troubleshooting the WiFi</b>
To resolve the issue, I logged back into the domain controller and navigated to the DHCP settings. I noticed that an IP address had been leased to the Windows 10 machine. To ensure the client had internet access, I enabled the Router option in the server settings and added the domain controller's IP address as the router. </br>
<img src="https://i.imgur.com/b3NzWeV.png" height="40%" width="40%" alt="Enabling Router"/> <br/>
After restarting the server, I verified that the Windows 10 machine now has internet access, successfully connecting to external networks through the domain controller. <br/>
<img src="https://i.imgur.com/SADPU8Q.png" height="40%" width="40%" alt="Gateway working"/> <br/>
From here, I was able to successfully ping Google from Command Prompt, confirming that our entire infrastructure is working as intended. This successful ping indicates that the Windows 10 client is fully connected to the network, able to communicate with external sites, and that the network configurations—DHCP, DNS, and routing—are all functioning correctly. <br/>
<img src="https://i.imgur.com/cnMtvAS.png" height="40%" width="40%" alt="Pinging Google"/> <br/>
Next, I renamed the Windows 10 machine to Client1 to ensure consistency with the network diagram. Now that the client machine is part of the domain, I was able to log in using my "Company credentials" effectively simulating how an employee would access their workstation in a corporate environment. Being part of the domain means the client machine now adheres to the domain's security policies and can access shared resources, receive updates, and be managed centrally. This setup mirrors the desired outcome for this lab project, where the Windows 10 client operates within the domain as it would in a real-world scenario.
 <br/>
<img src="https://i.imgur.com/FNBB5KL.png" height="40%" width="40%" alt="Using my own credentials"/> <br/>

