<p align="center">
  <img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1 align="center">Active Directory Deployment in Azure</h1>
<h2 align="center">On-Premises AD DS Configured Within Cloud-Based Virtual Machines</h2>

<h3 align="center">
Microsoft Azure | Windows Server | Active Directory | PowerShell
</h3>

<div align="center">
  
  ![Azure](https://img.shields.io/badge/Microsoft_Azure-0089D6?style=for-the-badge&logo=microsoft-azure&logoColor=white)
  ![Windows Server](https://img.shields.io/badge/Windows_Server-0078D6?style=for-the-badge&logo=windows&logoColor=white)
  ![Active Directory](https://img.shields.io/badge/Active_Directory-003366?style=for-the-badge)
  ![PowerShell](https://img.shields.io/badge/PowerShell-5391FE?style=for-the-badge&logo=powershell&logoColor=white)

</div>

---

## 📋 Project Overview

**Project Type:** Infrastructure Deployment & Identity Management  
**Status:** Complete and Fully Operational  
**Business Use Case:** Centralized identity, authentication, and access management in a cloud-hosted environment  

This project demonstrates the deployment of a traditional **on-premises Active Directory Domain Services (AD DS)** environment hosted entirely within **Microsoft Azure Virtual Machines**. The goal was to simulate a real-world enterprise identity infrastructure while leveraging cloud-based compute resources.

The implementation includes provisioning Azure VMs, promoting a Windows Server to a domain controller, configuring domain services, and validating authentication and domain join functionality for client machines.

This project is designed as a **portfolio-ready example** of enterprise identity and access management fundamentals.

---

## 🖥️ Environments & Technologies Used

- Microsoft Azure (Virtual Machines)
- Windows Server 2022 (Domain Controller)
- Windows 11 (Domain-Joined Client)
- Active Directory Domain Services (AD DS)
- Remote Desktop Protocol (RDP)
- PowerShell (administration & validation)

---

## ⚙️ High-Level Deployment Steps

1. Provisioned Azure virtual machines for domain controller and client systems  
2. Installed and configured Active Directory Domain Services  
3. Promoted Windows Server to a domain controller  
4. Joined client machine to the domain  
5. Validated authentication, directory services, and policy application  

---

## 🛠️ Deployment & Configuration

### Step 1: Provision Azure Virtual Machines

**Goal:** Create the Domain Controller (DC) and Client VMs in Azure, ensuring they are on the same network.

**Procedure:**

1. Sign in to the Azure Portal and create a new Resource Group named `AD-Lab-RG`.
2. Create the first VM (Domain Controller):
   - **Image:** Windows Server 2022
   - **Size:** Standard_B2s (or similar)
   - **Networking:** Place in a new Virtual Network (VNet) named `AD-VNet`.
3. Create the second VM (Client):
   - **Image:** Windows 11 Pro
   - **Networking:** Place in the **same** `AD-VNet` and Subnet as the DC.
4. Go to the Domain Controller's Network Interface and change the Private IP address assignment to **Static**.
5. Go to the Client VM's Network Interface and change the **DNS Servers** to Custom, entering the static Private IP of the Domain Controller.

![Creating the Domain Controller VM](images/step-01/step-01-dc-creation.png)
*(Add screenshot of the Azure VM creation Basics tab)*

![Setting the Static IP](images/step-01/step-01-static-ip.png)
*(Add screenshot of the DC's IP configuration showing Static assignment)*

![Setting Client DNS](images/step-01/step-01-client-dns.png)
*(Add screenshot of the Client VM's DNS settings pointing to the DC)*

**Result:** Both VMs are deployed and networked correctly, ready for AD DS installation.

---

### Step 2: Install Active Directory Domain Services (AD DS)

**Goal:** Install the AD DS role on the Windows Server VM.

**Procedure:**

1. Connect to the Domain Controller VM via RDP using the local Administrator account.
2. Open **Server Manager** and click **Add roles and features**.
3. Click **Next** through the wizard until you reach the **Server Roles** tab.
4. Check the box for **Active Directory Domain Services**.
5. Click **Add Features** on the popup, then click **Next** and **Install**.
6. Once the installation finishes, click the flag icon in Server Manager and select **Promote this server to a domain controller**.

![Server Manager Add Roles](images/step-02/step-02-add-roles.png)
*(Add screenshot of Server Manager dashboard)*

![Selecting AD DS](images/step-02/step-02-select-adds.png)
*(Add screenshot showing AD DS checked)*

**Result:** The AD DS role is installed, and the server is ready for promotion.

---

### Step 3: Promote Server to Domain Controller

**Goal:** Create a new forest and domain (e.g., `david.local`).

**Procedure:**

1. In the Deployment Configuration wizard, select **Add a new forest**.
2. Enter a Root domain name (e.g., `david.local`) and click **Next**.
3. Set the **Forest and Domain functional levels** to Windows Server 2016 or higher.
4. Ensure **DNS Server** and **Global Catalog (GC)** are checked.
5. Set a **DSRM (Directory Services Restore Mode)** password and click **Next**.
6. Click **Next** through the DNS and NetBIOS screens.
7. Verify the paths for the database, log files, and SYSVOL, then click **Next**.
8. Review the configuration and click **Install**. The server will automatically reboot.

![Domain Promotion Wizard](images/step-03/step-03-new-forest.png)
*(Add screenshot of the "Add a new forest" screen)*

![DSRM Password Setup](images/step-03/step-03-dsrm.png)
*(Add screenshot of the DSRM password screen)*

**Result:** The server reboots and is now a Domain Controller for the new forest.

---

### Step 4: Configure Organizational Units (OUs) and Admin User

**Goal:** Create a structured directory and a dedicated domain admin account.

**Procedure:**

1. After the reboot, log into the DC using the domain administrator account (`DAVID\Administrator`).
2. Open **Active Directory Users and Computers** from the Server Manager Tools menu.
3. Right-click your domain (e.g., `david.local`) → **New** → **Organizational Unit**.
4. Create OUs for `_HR`, `_IT`, `_Sales`, and `_Admins`.
5. Right-click the `_Admins` OU → **New** → **User**.
6. Create a new admin account (e.g., `David-Admin`), set the password, and choose "Password never expires" (for lab purposes only).
7. Right-click the new user → **Add to group** → Add them to the **Domain Admins** group.

![Creating OUs](images/step-04/step-04-create-ou.png)
*(Add screenshot of creating an OU)*

![Creating Admin User](images/step-04/step-04-create-user.png)
*(Add screenshot of the new user creation screen)*

![Adding to Domain Admins](images/step-04/step-04-domain-admins.png)
*(Add screenshot of adding the user to Domain Admins)*

**Result:** The directory structure is organized, and a dedicated admin account exists.

---

### Step 5: Join Windows 11 Client to the Domain

**Goal:** Connect the client VM to the newly created domain.

**Procedure:**

1. Connect to the Windows 11 Client VM via RDP.
2. Open **Settings** → **System** → **About** → **Advanced system settings**.
3. Under the **Computer Name** tab, click **Change**.
4. Select **Domain** and type your domain name (e.g., `david.local`). Click **OK**.
5. When prompted for credentials, enter the Domain Admin account created in Step 4 (e.g., `david.local\David-Admin`).
6. You should receive a "Welcome to the domain" message. Reboot the VM.
7. Log into the client machine using the domain admin credentials to verify it works.

![Changing Computer Name](images/step-05/step-05-change-domain.png)
*(Add screenshot of the "Domain" selection box)*

![Domain Join Success](images/step-05/step-05-welcome-domain.png)
*(Add screenshot of the "Welcome to the domain" popup)*

**Result:** The client machine is now a member of the Active Directory domain and can authenticate using domain credentials.

---

### Step 6: Create Bulk Users via PowerShell

**Goal:** Automate user creation to simulate real-world onboarding and save time.

**Procedure:**

1. On the Domain Controller, open **PowerShell ISE** as Administrator.
2. Create a CSV file with mock employee names and usernames (e.g., `users.csv`).
3. Write a PowerShell script to import the CSV and create the users in the `_HR` OU.
4. Run the script.
5. Open **Active Directory Users and Computers** and navigate to the `_HR` OU to verify the users were created successfully.

```powershell
# PowerShell script to create bulk users
Import-Module ActiveDirectory
$users = Import-Csv -Path "C:\Users\Administrator\Desktop\users.csv"

foreach ($user in $users) {
    New-ADUser -Name "$($user.FirstName) $($user.LastName)" `
    -GivenName $user.FirstName `
    -Surname $user.LastName `
    -SamAccountName $user.Username `
    -Path "OU=_HR,DC=david,DC=local" `
    -AccountPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force) `
    -Enabled $true}

https://images/step-06/step-06-powershell-script.png
(Add screenshot of PowerShell ISE with the script)

https://images/step-06/step-06-powershell-run.png
(Add screenshot of the console running the script without errors)

https://images/step-06/step-06-bulk-users.png
(Add screenshot of Active Directory showing the new users)

Result: Multiple users were provisioned efficiently using automation, demonstrating an understanding of script-based identity management.
