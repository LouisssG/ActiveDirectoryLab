# Active Directory Homelab Project

## Project Overview 🌟 
This project aims to set up an Active Directory (AD) environment using a homelab. The primary objectives are to learn about Active Directory, user management, group policies, and authentication services.

## Prerequisites ⚙️
- **Virtualization Software**: VirtualBox
- **Windows Server ISO**: For the AD domain controller.
- **Windows 10 ISO**: For client machines.
- Basic understanding of networking concepts.

## Environment Setup 🛠️ 

### Step 1: Install Virtualization Software 💻
1. Download and install your preferred virtualization software (e.g., VirtualBox)

    - [Install Virtualization Software (VirtualBox)](https://www.virtualbox.org/wiki/Downloads) 

### Step 2: Download ISO Files 📂
 1. [Windows 10 ISO File](https://www.microsoft.com/en-us/software-download/windows10)
 2. [Windows 2019 Server ISO File](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019)

### Step 3: Set Up Windows Server 💻

1. **Create a Virtual Machine**  
   Begin by creating a VM using the Windows Server ISO.

2. **Allocate Resources**  
   Allocate the necessary resources to the virtual machine to ensure optimal performance.

3. **Configure Network Settings**  
   Before proceeding with the installation, ensure that the network settings for the Windows Server 2019 VM are properly configured:
   - Open the settings for your Windows Server VM.
   - Under the **Network** section, ensure that:
     - **Adapter 1** is set to **NAT** to allow the VM to access the internet.
     - **Adapter 2** is set to **Internal Network** to facilitate communication with other VMs on the internal network.

   *Example of VM Network Settings*  
   ![VM Network Settings](https://i.imgur.com/GpJKz6r.png)

   ![VM Network Settings](https://i.imgur.com/GfpNEke.png)

5. **Install Windows Server**  
   Proceed with the installation of Windows Server, completing the initial configuration as prompted.  
   *Windows Server Installation Process*  
   ![Windows Setup](https://i.imgur.com/7OpPngK.png)


6. **Configure Network Connection**  
   Next, configure the network connection by following these steps:
   - Open **Network Connections**.
   - Identify which connection corresponds to the Internet and which one is for the internal network.  
       ![Network Connection](https://i.imgur.com/Xty8PkD.png)
   - Access Properties in the internal network
   - Select Internet protocol Version 4, then click properties.
   - Enter the following information as shown  
       ![IPv4 Properties](https://i.imgur.com/XbQjP2v.png)
    

### Step 4: Configure Active Directory 🌐
1. **Promote the Server to a Domain Controller**:
   - Open **Server Manager**.
   - Click on **Add roles and features**.
   - Select Server
   - Select **Active Directory Domain Services**.
   - Follow the wizard to install the role.
   - After installation, click on the notification flag in the top right and select **Promote this server to a domain controller**.
   - Choose **Add a new forest** and enter a domain name (e.g., `mydomain.com`).
   - Configure the domain controller options and finish the promotion.
   - Reboot the server.

### Step 5: Create Users and Groups 👥

1. **Create an Organizational Unit (OU)**  
   Begin by creating an OU to organize your administrative accounts. This helps in managing permissions and policies more effectively.  
   - Open **Active Directory Users and Computers** from the Tools menu in Server Manager.
   - Right-click on your domain, select **New**, then **Organizational Unit**.
   - Name the OU (e.g., "Admin Accounts").

2. **Create a Domain Admin User Account**  
   Next, create a user account and assign it to the Domain Admin group:
   - Right-click on the newly created OU, select **New**, then **User**.
   - Fill in the user details (e.g., first name, last name, username) and set a strong password.
   - After creating the user, right-click on the user account, select **Add to Group**.
   - In the dialog box, enter "Domain Admins" and click **OK** to assign the user to the Domain Admin group.  
        ![AD users and Computers](https://i.imgur.com/uTlMGHX.png)
  
You Can now sign into the machine using this **Admin** user!

### Step 6: Set Up RAS/NAT 🔄

1. **Add Roles and Features in Server Manager**  
   - In **Server Manager**, select **Add Roles and Features**.
   - Click **Next** until you reach the **Server Roles** section.

2. **Select Remote Access**  
   - Check **Remote Access** and click **Next**.
   - In the **Select Role Services** section, ensure you check **Routing** to enable routing functionalities.

3. **Install the Role**  
   - Click **Install** and wait for the process to complete.

4. **Configure Routing and Remote Access for NAT**  
   - Go to **Tools** > **Routing and Remote Access**.
   - Right-click your server name and select **Configure and Enable Routing and Remote Access**.
   - Follow the wizard to set up **Network Address Translation (NAT)**.
   - When prompted, choose the public interface to connect to the internet and select the appropriate internet network interface.  
   ![Routing and Remote Access NAT Configuration](https://i.imgur.com/mf62xGa.png)

5. **Expected Routing and Remote Access Configuration**  
   - After completing the configuration, the Routing and Remote Access console should show your server with the NAT configuration applied, indicated by a green icon next to your server name. The NAT setup manages traffic between your internal network and the internet, allowing communication with external networks.

   screenshot of the completed Routing and Remote Access configuration
   
   ![Routing and Remote Access Screenshot](https://i.imgur.com/1JOAxwl.png)  
   

### Step 7: Set Up DHCP 📡

1. **Add Roles and Features in Server Manager**  
   - In **Server Manager**, select **Add Roles and Features**.
   - Click **Next** until you reach the **Server Roles** section.

2. **Select DHCP Server**  
   - Check **DHCP Server** and click **Next**.
   - Follow the prompts to install the role.

3. **Configure DHCP Server and Create a New Scope**  
   - After installation, go to **Tools** > **DHCP** to open the DHCP Manager.
   - Right-click on your server name and select **New Scope**.
   - Follow the wizard to define the scope name, IP address range, subnet mask, and other settings.  
   ![IP Address Range Configuration](https://i.imgur.com/jmFG8k9.png)  
   *Screenshot of the IP address range configuration settings.*

4. **Verify DHCP Configuration in DHCP Window**  
   - In the DHCP Manager, you should see your server and the new scope listed under it. The status should show as **Active**, indicating that the scope is enabled and ready to assign IP addresses.  


### Step 8: Create Users with PowerShell 🔧

To automate the process of creating users, you can use a PowerShell script created by [Josh Madakor](https://www.youtube.com/@JoshMadakor)
. This script will create multiple users in Active Directory.

1. **Open PowerShell**  
   - Run PowerShell as an administrator.

2. **Prepare the Names File**  
   - You will need a text file containing a large number of names for the script to access. [Josh Madakor](https://www.youtube.com/@JoshMadakor) provides a text file with around 1,000 random names that you can use.

3. **Reference the PowerShell Script**  
   - Below is the PowerShell script that will create the users in Active Directory:

    ```powershell
   # ----- Edit these Variables for your own Use Case ----- #
   $PASSWORD_FOR_USERS   = "Password1"
   $USER_FIRST_LAST_LIST = Get-Content .\names.txt
   # ------------------------------------------------------ #

   $password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force
   New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false

   foreach ($n in $USER_FIRST_LAST_LIST) {
       $first = $n.Split(" ")[0].ToLower()
       $last = $n.Split(" ")[1].ToLower()
       $username = "$($first.Substring(0,1))$($last)".ToLower()
       Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
       
       New-AdUser -AccountPassword $password `
                  -GivenName $first `
                  -Surname $last `
                  -DisplayName $username `
                  -Name $username `
                  -EmployeeID $username `
                  -PasswordNeverExpires $true `
                  -Path "ou=_USERS,$(([ADSI]`"").distinguishedName)" `
                  -Enabled $true
   }

4. Verify User Creation  
After running the script, verify that the users were created successfully by:
- Checking the output in the PowerShell window to see the names of the created users.  
   ![PowerShell Output](https://i.imgur.com/ZbahagJ.png) 

- Opening the **Active Directory Users and Computers** application and navigating to the **USERS** folder in your domain. All the created users should be listed there.  
   ![Active Directory Users](https://i.imgur.com/RD60sM9.png) 



## Testing the Setup 🧪 

### Step 9: Configure Windows 10 Client 🖥️ 

1. **Create a VM for Windows 10**
   - Create a new virtual machine and select **Windows 10** as the operating system.
   - Allocate resources that make sense for your machine.
   - When configuring network settings, ensure **Adapter 1** is set to **Internal Network**. This setting will allow the Windows 10 client to communicate properly with the internal network and the Active Directory domain controller.  
     ![Network Settings](https://i.imgur.com/inSXypZ.png) <!-- Add a screenshot of the network settings configuration -->
   - Attach the Windows 10 ISO file to the virtual optical drive to begin the installation process.
   - Proceed through the Windows 10 installation.
   - Once installed, update the network adapter settings again if needed to ensure the client is using the internal network.

2. **Change the Hostname and Join the Domain**
   - Go to **Settings** > **System** > **About** and click on **Rename this PC (advanced)**.
   - In the **Computer Name/Domain Changes** window, enter the desired hostname for the Windows 10 client (in this case, I named it **Client1**).
   - Under **Member of**, select **Domain** and enter the domain name.
   - Enter the name and password of an account with permission to join the domain.

   ![Join Windows 10 to Domain](https://i.imgur.com/scMb8Yg.png) <!-- Add an image of the domain join process -->

### Step 10: Verify Network Connectivity 🔗

1. **Ping Google to Confirm Internet Access**
   - Open **Command Prompt** on the Windows 10 client.
   - Run the following command to test connectivity to the internet:
     ```
     ping google.com
     ```
   - If the ping is successful, it confirms that the network and NAT configuration on the domain controller was set up correctly, allowing the client to access the internet.


2. **Check DHCP Address Leases**
   - Go to the **DHCP** management console and navigate to the address leases within the scope that was created.
   - You should see **Client1** listed among the leased IP addresses, indicating successful communication between the DHCP server and the Windows 10 client.

   ![DHCP Address Leases](https://i.imgur.com/lgGo2tp.png) <!-- Add an image of the DHCP address leases -->

3. **Verify in Active Directory**
   - Open the **Active Directory Users and Computers** application.
   - You should see **Client1** listed under the **Computers** section, confirming that the client has successfully joined the domain.

   ![Active Directory Users](https://i.imgur.com/8135ncD.png) <!-- Add an image of the Active Directory Users and Computers -->

### Logging in with Created User Accounts 🔑
Now that everything is set up, you can log in to **Client1** using any of the 1,000 user accounts that were created. This can be done by selecting the "Other user" option on the login screen and entering the credentials of any of the users created through the PowerShell script.

![Login with User Account](path/to/login-image.png) <!-- Add an image of the login screen with user account -->

## Summary of Learning 📚
- Developed practical skills in setting up and managing Active Directory, including user and group management.
- Gained experience in configuring network settings and ensuring proper communication between virtual machines.
- Learned the importance of using PowerShell for automation in user account creation, enhancing efficiency in administrative tasks.

## Challenges Faced ⚠️ 
- Encountered an initial issue with running the PowerShell script to create user accounts. This required troubleshooting to ensure the script accessed the correct text file with user names.

## Conclusion ✅ 
This project has significantly enhanced my understanding of Active Directory and its critical components. By successfully setting up a Windows domain environment and managing user accounts, I am now better prepared for more advanced challenges in cybersecurity and IT administration. With all configurations in place, I can log in to Client1 using any of the 1,000 user accounts created through the PowerShell script, showcasing the practical application of my learning.



