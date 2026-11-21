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

I built this lab to practice the core Help Desk and Identity Management tasks I will perform daily, such as user provisioning, password resets, and account lockouts. It also demonstrates the foundational infrastructure and security skills required for an IAM Analyst role. The project showcases the deployment of a traditional on premises Active Directory Domain Services (AD DS) environment hosted entirely within Microsoft Azure Virtual Machines, simulating a real world enterprise identity infrastructure.

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

## ⚙️ Project Deployment Steps

1. Provisioned Azure virtual machines for domain controller and client systems  
2. Installed and configured Active Directory Domain Services  
3. Promoted the Windows Server to a domain controller  
4. Joined the client machine to the domain  
5. Automated user provisioning using PowerShell  
6. Performed Help Desk administration (password resets, account unlocks)  
7. Enforced security policies using Group Policy Objects (GPOs)  
8. Validated authentication, directory services, and policy application  

---

## 🛠️ Hands-On Deployment: From Azure Setup to Active Directory

### 1️⃣ Step 1: Provision Azure Virtual Machines

**Goal:** Create the Domain Controller (DC) and Client VMs in Azure, ensuring they are on the same network so they can communicate.

**Procedure:**

Before creating the virtual machines, I needed to set up the foundational network infrastructure. I created a Resource Group to hold everything, and a Virtual Network (VNet) so the two machines could talk to each other.

<img src="Images/Step 1/01-create-rg.png.png" width="100%" alt="Creating Resource Group">
<br>

**What I did:** Created a new Resource Group named Active-Directory-Lab to hold all the resources for this project.

---

<img src="Images/Step 1/02-create-vnet.png.png" width="100%" alt="Creating Virtual Network">
<br>

**What I did:** Provisioned a Virtual Network (VNet) to allow the Domain Controller and Client machines to communicate with each other securely.

---

Next, I configured the Domain Controller VM (DC-1). I selected Windows Server, set the administrator credentials, and allowed inbound RDP traffic so I could connect to it remotely.

<img src="Images/Step 1/03-dc1-basics.png.png" width="100%" alt="DC-1 Basics Tab">
<br>

**What I did:** Configured the Basics tab for the Domain Controller VM (DC-1) using Windows Server.

---

<img src="Images/Step 1/04-dc1-size-admin.png.png" width="100%" alt="DC-1 Size and Admin">
<br>

**What I did:** Selected the VM size and set the administrator credentials. I also allowed inbound RDP traffic so I could connect to it remotely.

---

<img src="Images/Step 1/05-dc1-networking.png.png" width="100%" alt="DC-1 Networking">
<br>

**What I did:** Placed DC-1 into the Virtual Network and assigned it a public IP for RDP access.

---

I repeated the same process for the Client VM (Client-1), placing it in the same VNet so it could communicate with the Domain Controller.

<img src="Images/Step 1/06-client1-basics.png.png" width="100%" alt="Client-1 Basics Tab">
<br>

**What I did:** Configured the Basics tab for the Client VM (Client-1).

---

<img src="Images/Step 1/07-client1-size-admin.png.png" width="100%" alt="Client-1 Size and Admin">
<br>

**What I did:** Set up the administrator credentials and network rules for Client-1.

---

<img src="Images/Step 1/08-client1-networking.png.png" width="100%" alt="Client-1 Networking">
<br>

**What I did:** Placed Client-1 in the same VNet as the Domain Controller so they can communicate.

---

Once both VMs were deployed, I confirmed they were running, and then configured the critical network settings. A Domain Controller must have a static IP for DNS to function properly.

<img src="Images/Step 1/09-vms-deployed.png.png" width="100%" alt="Both VMs Running">
<br>

**What I did:** Confirmed both VMs (DC-1 and Client-1) are successfully deployed and in a Running state.

---

<img src="Images/Step 1/10-dc1-static-ip.png.png" width="100%" alt="DC-1 Static IP Configuration">
<br>

**What I did:** Changed DC-1's private IP assignment to Static (10.0.0.4) so DNS can function properly.

---

Next, I pointed Client-1's DNS directly to DC-1 so it could find the domain. I verified the connection with a ping test.

<img src="Images/Step 1/11-client1-custom-dns.png.png" width="100%" alt="Client-1 Custom DNS">
<br>

**What I did:** Changed Client-1's DNS settings from "Inherit" to "Custom" and pointed it directly to DC-1's IP address.

---

<img src="Images/Step 1/12-client1-ping-test.png.png" width="100%" alt="Pinging DC-1">
<br>

**What I did:** Verified network connectivity by pinging DC-1's private IP address from Client-1.

---

Finally, I ran ipconfig on both machines to confirm the settings were applied correctly inside the operating systems.

<img src="Images/Step 1/13-client1-ipconfig.png.png" width="100%" alt="Client-1 ipconfig">
<br>

**What I did:** Ran ipconfig /all on Client-1 to confirm the DNS server is successfully pointing to DC-1.

---

<img src="Images/Step 1/14-dc1-ipconfig.png.png" width="100%" alt="DC-1 ipconfig">
<br>

**What I did:** Ran ipconfig /all on DC-1 to confirm its static IP and self referencing DNS settings.

**Result:** Both VMs are deployed and networked correctly, ready for AD DS installation.

---

### 2️⃣ Step 2: Install Active Directory Domain Services (AD DS)

**Goal:** Install the AD DS role on the Windows Server VM and promote it to a Domain Controller creating a new forest.

**Procedure:**

I logged into DC-1 via RDP and opened Server Manager to install the Active Directory Domain Services role. Once the installation succeeded, I launched the configuration wizard to promote the server.

<img src="Images/Step 2/01-adds-install-success.png.png" width="100%" alt="AD DS Installation Success">
<br>

**What I did:** Successfully installed the Active Directory Domain Services role via Server Manager.

---

During the promotion, I selected "Add a new forest" and defined the root domain name. After the server rebooted, I logged back in using the domain administrator format to confirm the domain was active.

<img src="Images/Step 2/02-new-forest.png.png" width="100%" alt="Adding a New Forest">
<br>

**What I did:** Launched the promotion wizard and selected "Add a new forest" to create a brand new domain environment.

---

<img src="Images/Step 2/03-domain-login-1.png.png" width="100%" alt="Domain Login Prompt">
<br>

**What I did:** After the automatic reboot, attempted to log back into the server.

---

<img src="Images/Step 2/04-domain-login-2.png.png" width="100%" alt="Domain Login Credentials">
<br>

**What I did:** Logged in using the domain administrator format (mydomain\userlab) to confirm the domain is active.

**Result:** The AD DS role is installed, and the server is now a Domain Controller for the new forest.

---

### 3️⃣ Step 3: Configure Organizational Units (OUs) and Admin User

**Goal:** Create a structured directory (OUs) and a dedicated Domain Admin account.

**Procedure:**

Using Active Directory Users and Computers (ADUC), I created a structured directory by building Organizational Units (OUs) to keep things organized.

<img src="Images/Step 3/01-create-ous.png.png" width="100%" alt="Creating OUs">
<br>

**What I did:** Opened Active Directory Users and Computers (ADUC) and created the _EMPLOYEES and _ADMINS Organizational Units.

---

Next, I created a new user, Jane Doe, inside the _ADMINS OU and added her to the Domain Admins security group.

<img src="Images/Step 3/02-create-jane-doe.png.png" width="100%" alt="Creating Jane Doe">
<br>

**What I did:** Created a new user, Jane Doe, inside the _ADMINS OU.

---

<img src="Images/Step 3/03-jane-domain-admins.png.png" width="100%" alt="Adding Jane to Domain Admins">
<br>

**What I did:** Added Jane Doe to the Domain Admins security group to grant her full administrative privileges.

---

I then created a _CLIENTS OU and moved the Client-1 computer object into it. Finally, I logged out and logged back in using the new dedicated admin account.

<img src="Images/Step 3/04-jane-admin-login.png.png" width="100%" alt="Logging in as Jane Admin">
<br>

**What I did:** Logged out and logged back in using the new dedicated admin account (mydomain\jane_admin).

---

<img src="Images/Step 3/05-clients-ou.png.png" width="100%" alt="Clients OU">
<br>

**What I did:** Created a _CLIENTS OU and moved the Client-1 computer object into it for better organization.

**Result:** The directory structure is organized, and a dedicated admin account exists.

---

### 4️⃣ Step 4: Create Bulk Users via PowerShell

**Goal:** Automate user creation to simulate real world onboarding and save time.

**Procedure:**

To simulate a real world onboarding process, I opened PowerShell ISE on DC-1 as an administrator and wrote a script to generate random users.

<img src="Images/Step 4/01-powershell-script.png.png" width="100%" alt="PowerShell Script">
<br>

**What I did:** Opened PowerShell ISE as an administrator and wrote a script to generate random users.

---

I ran the script and watched the console output as the accounts were created in real time. Afterwards, I opened ADUC to verify they were in the correct OU.

<img src="Images/Step 4/02-powershell-running.png.png" width="100%" alt="Running the Script">
<br>

**What I did:** Executed the script and watched the console output as the accounts were created in real time.

---

<img src="Images/Step 4/03-users-in-aduc.png.png" width="100%" alt="Users in ADUC">
<br>

**What I did:** Opened ADUC to verify that the _EMPLOYEES OU is now populated with the newly generated accounts.

**Result:** Multiple users were provisioned efficiently using automation, demonstrating an understanding of script based identity management.

---

### 5️⃣ Step 5: Remote Desktop and Client Authentication

**Goal:** Allow standard domain users to log into Client-1 via RDP and verify the login works.

**Procedure:**

By default, only admins can RDP into a machine. I logged into Client-1 as jane_admin and added the "Domain Users" group to the Remote Desktop Users list.

<img src="Images/Step 5/01-rdp-domain-users.png.png" width="100%" alt="Adding Domain Users to RDP">
<br>

**What I did:** Logged into Client-1 as jane_admin and added the "Domain Users" group to the Remote Desktop Users list.

---

I then logged into Client-1 as jane_admin to verify, and subsequently attempted to log in with one of the newly generated standard users (babe.tow). After bypassing the certificate warning, I successfully logged in and used the whoami command to prove the active session was indeed the standard user.

<img src="Images/Step 5/02-login-client1-jane.png.png" width="100%" alt="Logging in as Jane Admin">
<br>

**What I did:** Verified administrative access to Client-1.

---

<img src="Images/Step 5/03-login-babe-tow.png.png" width="100%" alt="Logging in as Babe Tow">
<br>

**What I did:** Attempted to log in with a newly generated standard user (babe.tow) to test the new permissions.

---

<img src="Images/Step 5/04-login-babe-tow-retry.png.png" width="100%" alt="Babe Tow Login Retry">
<br>

**What I did:** Entered the standard user's credentials at the Windows Security prompt.

---

<img src="Images/Step 5/05-rdp-cert-warning.png.png" width="100%" alt="RDP Certificate Warning">
<br>

**What I did:** Bypassed the standard self signed certificate warning to complete the remote connection.

---

<img src="Images/Step 5/06-babe-tow-success-whoami.png.png" width="100%" alt="Successful Login and whoami">
<br>

**What I did:** Successfully logged in. Used the whoami command to prove the active session is indeed the standard user.

**Result:** Non administrative domain users can now access the client machine remotely.

---

### 6️⃣ Step 6: Group Policy & Account Lockout

**Goal:** Implement a Group Policy to lock accounts after 5 failed login attempts then test it.

**Procedure:**

I opened Group Policy Management on DC-1 and configured the Account Lockout Policy to trigger after 5 invalid attempts.

<img src="Images/Step 6/01-lockout-policy.png.png" width="100%" alt="Configuring Lockout Policy">
<br>

**What I did:** Opened Group Policy Management and configured the Account Lockout Policy to trigger after 5 invalid attempts.

---

To test this, I went to Client-1 and intentionally entered a bad password for babe.tow until the system triggered the lockout error.

<img src="Images/Step 6/02-failed-login-attempt.png.png" width="100%" alt="Failed Login Attempt">
<br>

**What I did:** Went to Client-1 and intentionally entered a bad password multiple times to trigger the policy.

---

<img src="Images/Step 6/03-account-locked-error.png.png" width="100%" alt="Account Locked Error">
<br>

**What I did:** Received the lockout error message, confirming the Group Policy is working.

---

As a Help Desk task, I went back to DC-1, found the locked account in ADUC, and checked the "Unlock account" box. I also performed a password reset.

<img src="Images/Step 6/04-unlock-account.png.png" width="100%" alt="Unlocking the Account">
<br>

**What I did:** Returned to DC-1 and checked the "Unlock account" box in ADUC to resolve the Help Desk ticket.

---

<img src="Images/Step 6/05-reset-password-menu.png.png" width="100%" alt="Reset Password Menu">
<br>

**What I did:** Right clicked the user to access the Reset Password tool.

---

<img src="Images/Step 6/06-reset-password-dialog.png.png" width="100%" alt="Reset Password Dialog">
<br>

**What I did:** Set a new temporary password for the user.

**Result:** Successfully enforced a security policy and performed routine Help Desk administration.

---

### 7️⃣ Step 7: Disabling Accounts and Reviewing Event Logs

**Goal:** Disable an account, test the login, re enable it, and review the security logs to prove the actions were recorded.

**Procedure:**

I disabled the babe.tow account in ADUC to simulate a terminated employee.

<img src="Images/Step 7/01-disable-account.png.png" width="100%" alt="Disabling Account">
<br>

**What I did:** Right clicked the user in ADUC and selected "Disable Account" to simulate a terminated employee.

---

Attempting to log into Client-1 resulted in a specific "Account is currently disabled" error message.

<img src="Images/Step 7/02-disabled-login-error.png.png" width="100%" alt="Disabled Login Error">
<br>

**What I did:** Attempted to log into Client-1 and received the specific "Account is currently disabled" error.

---

I re enabled the account and verified that login was restored. I also captured a UAC prompt showing a standard user needing to provide admin credentials to authorize a system change, demonstrating least privilege.

<img src="Images/Step 7/03-enable-account.png.png" width="100%" alt="Enabling Account">
<br>

**What I did:** Right clicked the user again and selected "Enable Account" to restore their access.

---

<img src="Images/Step 7/04-account-enabled.png.png" width="100%" alt="Account Enabled">
<br>

**What I did:** Confirmed the account has been successfully re enabled.

---

<img src="Images/Step 7/05-reenabled-login-success.png.png" width="100%" alt="Re-enabled Login Success">
<br>

**What I did:** Verified that the user can successfully log back into Client-1.

---

<img src="Images/Step 7/06-uac-privilege-escalation.png.png" width="100%" alt="UAC Prompt">
<br>

**What I did:** Demonstrated least privilege. A standard user needs to provide admin credentials (jane_admin) to authorize a system change.

---

Finally, I opened Event Viewer on Client-1 and reviewed the Security logs. I found Event ID 4625, which explicitly showed the account lockout and the bad password attempts.

<img src="Images/Step 7/07-event-4625-lockout.png.png" width="100%" alt="Event 4625 Lockout">
<br>

**What I did:** Opened Event Viewer on Client-1 and found Event ID 4625, which explicitly logs the Account Lockout.

---

<img src="Images/Step 7/08-event-4625-bad-password.png.png" width="100%" alt="Event 4625 Bad Password">
<br>

**What I did:** Reviewed the details of the event log to see the failure reason was a bad password.

---

<img src="Images/Step 7/09-event-log-sequence.png.png" width="100%" alt="Event Log Sequence">
<br>

**What I did:** Observed the chronological sequence of failed logon events that led to the final account lockout.

**Result:** Successfully demonstrated the ability to audit and troubleshoot security events at the system level.

---

## 🔐 Validation & Testing

To ensure the environment is fully operational and secure, I performed the following validation steps:

- Verified domain controller health and DNS functionality.
- Confirmed successful user authentication by logging into the Client VM using a domain user account.
- Validated the directory structure and domain trust.
- Ensured stable communication between the domain controller and client systems.
- Tested Help Desk scenarios (password resets, account lockouts) and verified Group Policy enforcement on the client VM.

---

## 🧠 Skills Demonstrated

- **Help Desk Administration:** Managing user accounts, performing password resets, unlocking accounts, and documenting step by step resolutions.
- **Troubleshooting & Auditing:** Using Event Viewer (Event ID 4625) to audit security logs, verify authentication, and diagnose account lockouts.
- **Identity & Access Management (IAM):** Creating Organizational Units (OUs), managing user lifecycles, configuring Role Based Access Control (RBAC), and enforcing security policies.
- **Group Policy Management:** Creating, linking, and enforcing GPOs to secure user environments.
- **Automation:** Using PowerShell to automate bulk user creation.
- **Windows Server Administration:** Active Directory Domain Services (AD DS) installation and promotion.
- **Cloud Infrastructure:** Azure Virtual Machine provisioning, networking, and DNS configuration.

---

## ✅ Outcome

A fully functional Active Directory environment was successfully deployed within Microsoft Azure, simulating a real world on premises identity infrastructure in a cloud hosted setting. This project demonstrates my practical experience with enterprise authentication systems, Windows Server administration, Help Desk account management, identity and access management (IAM), and cloud based infrastructure.
