<p align="center">
  <img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1 align="center">Active Directory Deployment & Administration in Azure</h1>
<h2 align="center">Provisioning, Identity Management, and Group Policy Enforcement</h2>

<h3 align="center">
Microsoft Azure | Windows Server | Active Directory | PowerShell | Group Policy
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
**Business Use Case:** Centralized identity, authentication, and access management in a cloud hosted environment  

I built this lab to practice the exact identity management tasks I would perform as an IAM Analyst or Help Desk technician. It demonstrates the deployment of a traditional on premises Active Directory Domain Services (AD DS) environment hosted entirely within Microsoft Azure Virtual Machines, simulating a real world enterprise identity infrastructure using cloud based compute resources.

The implementation includes provisioning Azure VMs, promoting a Windows Server to a domain controller, configuring domain services, validating authentication for client machines, and performing advanced administrative tasks such as PowerShell automation and Group Policy enforcement.

---

## 🖥️ Environments & Technologies Used

- Microsoft Azure (Virtual Machines & Networking)
- Windows Server 2022 (Domain Controller)
- Windows 11 (Domain Joined Client)
- Active Directory Domain Services (AD DS)
- Group Policy Management
- Remote Desktop Protocol (RDP)
- PowerShell (Administration & Automation)

---

## ⚙️ High Level Deployment Steps

1. Provisioned Azure virtual machines for domain controller and client systems  
2. Installed and configured Active Directory Domain Services  
3. Promoted Windows Server to a domain controller  
4. Joined client machine to the domain  
5. Automated user provisioning using PowerShell  
6. Performed Help Desk administration (password resets, account unlocks)  
7. Enforced security policies using Group Policy Objects (GPOs)  
8. Validated authentication, directory services, and policy application  

---

## 🛠️ Deployment & Configuration

### Step 1: Provision Azure Virtual Machines

**Goal:** Create the Domain Controller (DC) and Client VMs in Azure, ensuring they are on the same network so they can communicate.

**Procedure:**

<img src="images/step-1/01-create-rg.png" width="80%" alt="Creating Resource Group">
<br>
*Creating a new Resource Group named Active-Directory-Lab to hold all the resources for this project.*

<img src="images/step-1/02-create-vnet.png" width="80%" alt="Creating Virtual Network">
<br>
*Provisioning a Virtual Network (VNet) to allow the Domain Controller and Client machines to communicate with each other securely.*

<img src="images/step-1/03-dc1-basics.png" width="80%" alt="DC-1 Basics Tab">
<br>
*Configuring the Basics tab for the Domain Controller VM (DC-1) using Windows Server.*

<img src="images/step-1/04-dc1-size-admin.png" width="80%" alt="DC-1 Size and Admin">
<br>
*Selecting the VM size and setting the administrator credentials. I also allowed inbound RDP traffic so I could connect to it remotely.*

<img src="images/step-1/05-dc1-networking.png" width="80%" alt="DC-1 Networking">
<br>
*Placing DC-1 into the Virtual Network and assigning it a public IP for RDP access.*

<img src="images/step-1/06-client1-basics.png" width="80%" alt="Client-1 Basics Tab">
<br>
*Configuring the Basics tab for the Client VM (Client-1).*

<img src="images/step-1/07-client1-size-admin.png" width="80%" alt="Client-1 Size and Admin">
<br>
*Setting up the administrator credentials and network rules for Client-1.*

<img src="images/step-1/08-client1-networking.png" width="80%" alt="Client-1 Networking">
<br>
*Placing Client-1 in the same VNet as the Domain Controller so they can communicate.*

<img src="images/step-1/09-vms-deployed.png" width="80%" alt="Both VMs Running">
<br>
*Confirming both VMs (DC-1 and Client-1) are successfully deployed and in a Running state.*

<img src="images/step-1/10-dc1-static-ip.png" width="80%" alt="DC-1 Static IP Configuration">
<br>
*Changing DC-1's private IP assignment to Static (10.0.0.4). A Domain Controller must have a static IP for DNS to function properly.*

<img src="images/step-1/11-client1-custom-dns.png" width="80%" alt="Client-1 Custom DNS">
<br>
*Changing Client-1's DNS settings from "Inherit" to "Custom" and pointing it directly to DC-1's IP address.*

<img src="images/step-1/12-client1-ping-test.png" width="80%" alt="Pinging DC-1">
<br>
*Verifying network connectivity by pinging DC-1's private IP address from Client-1.*

<img src="images/step-1/13-client1-ipconfig.png" width="80%" alt="Client-1 ipconfig">
<br>
*Running ipconfig /all on Client-1 to confirm the DNS server is successfully pointing to DC-1.*

<img src="images/step-1/14-dc1-ipconfig.png" width="80%" alt="DC-1 ipconfig">
<br>
*Running ipconfig /all on DC-1 to confirm its static IP and self referencing DNS settings.*

**Result:** Both VMs are deployed and networked correctly, ready for AD DS installation.

---

### Step 2: Install Active Directory Domain Services (AD DS)

**Goal:** Install the AD DS role on the Windows Server VM and promote it to a Domain Controller creating a new forest.

**Procedure:**

<img src="images/step-2/01-adds-install-success.png" width="80%" alt="AD DS Installation Success">
<br>
*Successfully installing the Active Directory Domain Services role via Server Manager.*

<img src="images/step-2/02-new-forest.png" width="80%" alt="Adding a New Forest">
<br>
*Launching the promotion wizard and selecting "Add a new forest" to create a brand new domain environment.*

<img src="images/step-2/03-domain-login-1.png" width="80%" alt="Domain Login Prompt">
<br>
*After the automatic reboot, attempting to log back into the server.*

<img src="images/step-2/04-domain-login-2.png" width="80%" alt="Domain Login Credentials">
<br>
*Logging in using the domain administrator format (mydomain\userlab) to confirm the domain is active.*

**Result:** The AD DS role is installed, and the server is now a Domain Controller for the new forest.

---

### Step 3: Configure Organizational Units (OUs) and Admin User

**Goal:** Create a structured directory (OUs) and a dedicated Domain Admin account.

**Procedure:**

<img src="images/step-3/01-create-ous.png" width="80%" alt="Creating OUs">
<br>
*Opening Active Directory Users and Computers (ADUC) and creating the _EMPLOYEES and _ADMINS Organizational Units.*

<img src="images/step-3/02-create-jane-doe.png" width="80%" alt="Creating Jane Doe">
<br>
*Creating a new user, Jane Doe, inside the _ADMINS OU.*

<img src="images/step-3/03-jane-domain-admins.png" width="80%" alt="Adding Jane to Domain Admins">
<br>
*Adding Jane Doe to the Domain Admins security group to grant her full administrative privileges.*

<img src="images/step-3/04-jane-admin-login.png" width="80%" alt="Logging in as Jane Admin">
<br>
*Logging out and logging back in using the new dedicated admin account (mydomain\jane_admin).*

<img src="images/step-3/05-clients-ou.png" width="80%" alt="Clients OU">
<br>
*Creating a _CLIENTS OU and moving the Client-1 computer object into it for better organization.*

**Result:** The directory structure is organized, and a dedicated admin account exists.

---

### Step 4: Create Bulk Users via PowerShell

**Goal:** Automate user creation to simulate real world onboarding and save time.

**Procedure:**

<img src="images/step-4/01-powershell-script.png" width="80%" alt="PowerShell Script">
<br>
*Opening PowerShell ISE as an administrator and writing a script to generate random users.*

<img src="images/step-4/02-powershell-running.png" width="80%" alt="Running the Script">
<br>
*Executing the script and watching the console output as the accounts are created in real time.*

<img src="images/step-4/03-users-in-aduc.png" width="80%" alt="Users in ADUC">
<br>
*Opening ADUC to verify that the _EMPLOYEES OU is now populated with the newly generated accounts.*

**Result:** Multiple users were provisioned efficiently using automation, demonstrating an understanding of script based identity management.

---

### Step 5: Remote Desktop and Client Authentication

**Goal:** Allow standard domain users to log into Client-1 via RDP and verify the login works.

**Procedure:**

<img src="images/step-5/01-rdp-domain-users.png" width="80%" alt="Adding Domain Users to RDP">
<br>
*Logging into Client-1 as jane_admin and adding the "Domain Users" group to the Remote Desktop Users list.*

<img src="images/step-5/02-login-client1-jane.png" width="80%" alt="Logging in as Jane Admin">
<br>
*Verifying administrative access to Client-1.*

<img src="images/step-5/03-login-babe-tow.png" width="80%" alt="Logging in as Babe Tow">
<br>
*Attempting to log in with a newly generated standard user (babe.tow) to test the new permissions.*

<img src="images/step-5/04-login-babe-tow-retry.png" width="80%" alt="Babe Tow Login Retry">
<br>
*Entering the standard user's credentials at the Windows Security prompt.*

<img src="images/step-5/05-rdp-cert-warning.png" width="80%" alt="RDP Certificate Warning">
<br>
*Bypassing the standard self signed certificate warning to complete the remote connection.*

<img src="images/step-5/06-babe-tow-success-whoami.png" width="80%" alt="Successful Login and whoami">
<br>
*Successfully logged in. Using the whoami command to prove the active session is indeed the standard user.*

**Result:** Non administrative domain users can now access the client machine remotely.

---

### Step 6: Group Policy & Account Lockout

**Goal:** Implement a Group Policy to lock accounts after 5 failed login attempts then test it.

**Procedure:**

<img src="images/step-6/01-lockout-policy.png" width="80%" alt="Configuring Lockout Policy">
<br>
*Opening Group Policy Management and configuring the Account Lockout Policy to trigger after 5 invalid attempts.*

<img src="images/step-6/02-failed-login-attempt.png" width="80%" alt="Failed Login Attempt">
<br>
*Going to Client-1 and intentionally entering a bad password multiple times to trigger the policy.*

<img src="images/step-6/03-account-locked-error.png" width="80%" alt="Account Locked Error">
<br>
*Receiving the lockout error message, confirming the Group Policy is working.*

<img src="images/step-6/04-unlock-account.png" width="80%" alt="Unlocking the Account">
<br>
*Returning to DC-1 and checking the "Unlock account" box in ADUC to resolve the Help Desk ticket.*

<img src="images/step-6/05-reset-password-menu.png" width="80%" alt="Reset Password Menu">
<br>
*Right clicking the user to access the Reset Password tool.*

<img src="images/step-6/06-reset-password-dialog.png" width="80%" alt="Reset Password Dialog">
<br>
*Setting a new temporary password for the user.*

**Result:** Successfully enforced a security policy and performed routine Help Desk administration.

---

### Step 7: Disabling Accounts and Reviewing Event Logs

**Goal:** Disable an account, test the login, re enable it, and review the security logs to prove the actions were recorded.

**Procedure:**

<img src="images/step-7/01-disable-account.png" width="80%" alt="Disabling Account">
<br>
*Right clicking the user in ADUC and selecting "Disable Account" to simulate a terminated employee.*

<img src="images/step-7/02-disabled-login-error.png" width="80%" alt="Disabled Login Error">
<br>
*Attempting to log into Client-1 and receiving the specific "Account is currently disabled" error.*

<img src="images/step-7/03-enable-account.png" width="80%" alt="Enabling Account">
<br>
*Right clicking the user again and selecting "Enable Account" to restore their access.*

<img src="images/step-7/04-account-enabled.png" width="80%" alt="Account Enabled">
<br>
*Confirming the account has been successfully re enabled.*

<img src="images/step-7/05-reenabled-login-success.png" width="80%" alt="Re-enabled Login Success">
<br>
*Verifying that the user can successfully log back into Client-1.*

<img src="images/step-7/06-uac-privilege-escalation.png" width="80%" alt="UAC Prompt">
<br>
*Demonstrating least privilege. A standard user needs to provide admin credentials (jane_admin) to authorize a system change.*

<img src="images/step-7/07-event-4625-lockout.png" width="80%" alt="Event 4625 Lockout">
<br>
*Opening Event Viewer on Client-1 and finding Event ID 4625, which explicitly logs the Account Lockout.*

<img src="images/step-7/08-event-4625-bad-password.png" width="80%" alt="Event 4625 Bad Password">
<br>
*Reviewing the details of the event log to see the failure reason was a bad password.*

<img src="images/step-7/09-event-log-sequence.png" width="80%" alt="Event Log Sequence">
<br>
*Observing the chronological sequence of failed logon events that led to the final account lockout.*

**Result:** Successfully demonstrated the ability to audit and troubleshoot security events at the system level.

---

## 🔐 Validation & Testing

To ensure the environment is fully operational and secure, the following validation steps were performed:

- Verified domain controller health and DNS functionality.
- Confirmed successful user authentication by logging into the Client VM using a domain user account.
- Validated the directory structure and domain trust.
- Ensured stable communication between the domain controller and client systems.
- Tested Help Desk scenarios (password resets, account lockouts) and verified Group Policy enforcement on the client VM.

---

## 🧠 Skills Demonstrated

- **Cloud Infrastructure:** Azure Virtual Machine provisioning, networking, and DNS configuration.
- **Windows Server Administration:** Active Directory Domain Services (AD DS) installation and promotion.
- **Identity & Access Management (IAM):** Creating Organizational Units (OUs), managing user accounts, configuring Role Based Access Control (RBAC), and performing password resets/unlocks.
- **Group Policy Management:** Creating, linking, and enforcing GPOs to secure user environments.
- **Automation:** Using PowerShell to automate bulk user creation.
- **Troubleshooting:** Validating domain connectivity, authentication, and DNS resolution.

---

## ✅ Outcome

A fully functional Active Directory environment was successfully deployed within Microsoft Azure, simulating a real world on premises identity infrastructure in a cloud hosted setting. This project demonstrates my practical experience with enterprise authentication systems, Windows Server administration, identity management, and cloud based infrastructure management.




















<p align="center">
  <img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1 align="center">Active Directory Deployment & Administration in Azure</h1>
<h2 align="center">Provisioning, Identity Management, and Group Policy Enforcement</h2>

<h3 align="center">
Microsoft Azure | Windows Server | Active Directory | PowerShell | Group Policy
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

I built this lab to practice the exact identity management tasks I would perform as an IAM Analyst or Help Desk technician. It demonstrates the deployment of a traditional **on-premises Active Directory Domain Services (AD DS)** environment hosted entirely within **Microsoft Azure Virtual Machines**, simulating a real-world enterprise identity infrastructure using cloud-based compute resources.

The implementation includes provisioning Azure VMs, promoting a Windows Server to a domain controller, configuring domain services, validating authentication for client machines, and performing advanced administrative tasks such as PowerShell automation and Group Policy enforcement.

---

## 🖥️ Environments & Technologies Used

- Microsoft Azure (Virtual Machines & Networking)
- Windows Server 2022 (Domain Controller)
- Windows 11 (Domain-Joined Client)
- Active Directory Domain Services (AD DS)
- Group Policy Management
- Remote Desktop Protocol (RDP)
- PowerShell (Administration & Automation)

---

## ⚙️ High-Level Deployment Steps

1. Provisioned Azure virtual machines for domain controller and client systems  
2. Installed and configured Active Directory Domain Services  
3. Promoted Windows Server to a domain controller  
4. Joined client machine to the domain  
5. Automated user provisioning using PowerShell  
6. Performed Help Desk administration (password resets, account unlocks)  
7. Enforced security policies using Group Policy Objects (GPOs)  
8. Validated authentication, directory services, and policy application  

---

## 🛠️ Deployment & Configuration

### Step 1: Provision Azure Virtual Machines

**Goal:** Create the Domain Controller (DC) and Client VMs in Azure, ensuring they are on the same network so they can communicate.

**Procedure:**

1. Sign in to the Azure Portal and create a new Resource Group named `Active-Directory-Lab`.
2. Create the first VM (Domain Controller):
   - **Image:** Windows Server 2022
   - **Size:** Standard_B2s (or similar)
   - **Networking:** Place in a new Virtual Network (VNet) named `Active-Directory-VNet`.
3. Create the second VM (Client):
   - **Image:** Windows 11 Pro
   - **Networking:** Place in the **same** `AD-VNet` and Subnet as the DC.
4. Go to the Domain Controller's Network Interface and change the Private IP address assignment to **Static** (this is required for DNS to function properly).
5. Go to the Client VM's Network Interface and change the **DNS Servers** to Custom, entering the static Private IP of the Domain Controller.

<img src="images/step-01/step-01-dc-creation.png" alt="Creating the Domain Controller VM" width="80%">

*(Screenshot of the Azure VM creation Basics tab)*

<img src="images/step-01/step-01-static-ip.png" alt="Setting the Static IP" width="80%">

*(Screenshot of the DC's IP configuration showing Static assignment)*

<img src="images/step-01/step-01-client-dns.png" alt="Setting Client DNS" width="80%">

*(Screenshot of the Client VM's DNS settings pointing to the DC)*

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

<img src="images/step-02/step-02-add-roles.png" alt="Server Manager Add Roles" width="80%">

*(Screenshot of Server Manager dashboard)*

<img src="images/step-02/step-02-select-adds.png" alt="Selecting AD DS" width="80%">

*(Screenshot showing AD DS checked)*

**Result:** The AD DS role is installed, and the server is ready for promotion.

---

### Step 3: Promote Server to Domain Controller

**Goal:** Create a new forest and domain.

**Procedure:**

1. In the Deployment Configuration wizard, select **Add a new forest**.
2. Enter a Root domain name (e.g., `david.local`) and click **Next**.
3. Set the **Forest and Domain functional levels** to Windows Server 2016 or higher.
4. Ensure **DNS Server** and **Global Catalog (GC)** are checked.
5. Set a **DSRM (Directory Services Restore Mode)** password and click **Next**.
6. Click **Next** through the DNS and NetBIOS screens.
7. Verify the paths for the database, log files, and SYSVOL, then click **Next**.
8. Review the configuration and click **Install**. The server will automatically reboot.

<img src="images/step-03/step-03-new-forest.png" alt="Domain Promotion Wizard" width="80%">

*(Screenshot of the "Add a new forest" screen)*

<img src="images/step-03/step-03-dsrm.png" alt="DSRM Password Setup" width="80%">

*(Screenshot of the DSRM password screen)*

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

<img src="images/step-04/step-04-create-ou.png" alt="Creating OUs" width="80%">

*(Screenshot of creating an OU)*

<img src="images/step-04/step-04-create-user.png" alt="Creating Admin User" width="80%">

*(Screenshot of the new user creation screen)*

<img src="images/step-04/step-04-domain-admins.png" alt="Adding to Domain Admins" width="80%">

*(Screenshot of adding the user to Domain Admins)*

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

<img src="images/step-05/step-05-change-domain.png" alt="Changing Computer Name" width="80%">

*(Screenshot of the "Domain" selection box)*

<img src="images/step-05/step-05-welcome-domain.png" alt="Domain Join Success" width="80%">

*(Screenshot of the "Welcome to the domain" popup)*

<img src="images/step-07/step-07-domain-login.png" alt="Client Domain Login" width="80%">

*(Screenshot of the Windows 11 login screen showing the domain user name, e.g., DAVID\David-Admin)*

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

<pre><code>
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
    -Enabled $true
}
</code></pre>

<img src="images/step-06/step-06-powershell-script.png" alt="PowerShell Script Execution" width="80%">

*(Screenshot of PowerShell ISE with the script)*

<img src="images/step-06/step-06-powershell-run.png" alt="PowerShell Script Running Successfully" width="80%">

*(Screenshot of the console running the script without errors)*

<img src="images/step-06/step-06-bulk-users.png" alt="Bulk Users Created in ADUC" width="80%">

*(Screenshot of Active Directory showing the new users)*

**Result:** Multiple users were provisioned efficiently using automation, demonstrating an understanding of script-based identity management.

---

### Step 7: Advanced AD Administration (Help Desk Tasks)

**Goal:** Simulate common daily Help Desk tasks such as resetting a user's password and unlocking a locked account.

**Procedure:**

1. On the Domain Controller, open **Active Directory Users and Computers**.
2. Navigate to the `_HR` Organizational Unit.
3. Right-click on one of the bulk users you created (e.g., John Doe) and select **Reset Password**.
4. Enter a new temporary password, check the box for **"User must change password at next logon"**, and click **OK**.

<img src="images/step-07/step-07-reset-password.png" alt="Reset Password Dialog" width="80%">

*(Screenshot of the Reset Password dialog box)*

5. Next, simulate a locked account. Open the Client VM, and attempt to log in with a user's credentials but enter the wrong password 3-5 times (depending on your domain lockout policy).
6. On the Domain Controller, refresh ADUC. The user will now have a lock icon on their account.
7. Right-click the user, select **Unlock account**, and click **OK**.

<img src="images/step-07/step-07-unlock-account.png" alt="Unlocking User Account" width="80%">

*(Screenshot of the account lockout icon and the unlock dialog)*

**Result:** Successfully performed routine identity management tasks that are critical for Help Desk support.

---

### Step 8: Configure Group Policy (GPO)

**Goal:** Enforce a security policy across the `_HR` department using Group Policy Management.

**Procedure:**

1. On the Domain Controller, open **Group Policy Management** from the Server Manager Tools menu.
2. Right-click the `_HR` Organizational Unit and select **Create a GPO in this domain, and Link it here...**. Name it "HR Control Panel Restriction", then right-click it and select **Edit**.
3. Navigate to **User Configuration** → **Policies** → **Administrative Templates** → **Control Panel**.
4. Double-click the policy named **"Prohibit access to Control Panel and PC settings"**.
5. Select **Enabled**, click **Apply**, and then **OK**.
6. Close the Group Policy Management Editor.

<img src="images/step-08/step-08-gpo-editor.png" alt="Editing GPO" width="80%">

*(Screenshot of the Group Policy Editor showing the Control Panel policy enabled)*

<img src="images/step-08/step-08-link-gpo.png" alt="Linking GPO to OU" width="80%">

*(Screenshot of the GPO linked to the _HR OU)*

7. On the Client VM, open Command Prompt as Administrator and run `gpupdate /force` to apply the policy.
8. Log in as an `_HR` user, and try to open the Control Panel. You should see a restriction message.

<img src="images/step-08/step-08-gpo-verified.png" alt="GPO Policy Verified" width="80%">

*(Screenshot of the restriction message on the Client VM)*

**Result:** Successfully deployed and verified a Group Policy Object (GPO) to restrict user access, demonstrating an understanding of centralized security management.

---

## 🔐 Validation & Testing

To ensure the environment is fully operational and secure, the following validation steps were performed:

- Verified domain controller health and DNS functionality.
- Confirmed successful user authentication by logging into the Client VM using a domain user account.
- Validated the directory structure and domain trust.
- Ensured stable communication between the domain controller and client systems.
- Tested Help Desk scenarios (password resets, account lockouts) and verified Group Policy enforcement on the client VM.

---

## 🧠 Skills Demonstrated

- **Cloud Infrastructure:** Azure Virtual Machine provisioning, networking, and DNS configuration.
- **Windows Server Administration:** Active Directory Domain Services (AD DS) installation and promotion.
- **Identity & Access Management (IAM):** Creating Organizational Units (OUs), managing user accounts, configuring Role-Based Access Control (RBAC), and performing password resets/unlocks.
- **Group Policy Management:** Creating, linking, and enforcing GPOs to secure user environments.
- **Automation:** Using PowerShell to automate bulk user creation.
- **Troubleshooting:** Validating domain connectivity, authentication, and DNS resolution.

---

## ✅ Outcome

A fully functional Active Directory environment was successfully deployed within Microsoft Azure, simulating a real-world on-premises identity infrastructure in a cloud-hosted setting. This project demonstrates my practical experience with enterprise authentication systems, Windows Server administration, identity management, and cloud-based infrastructure management.
