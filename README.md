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

### 1️⃣ Step 1: Provision Azure Virtual Machines

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

### 2️⃣ Step 2: Install Active Directory Domain Services (AD DS)

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

### 3️⃣ Step 3: Configure Organizational Units (OUs) and Admin User

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

### 4️⃣ Step 4: Create Bulk Users via PowerShell

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

### 5️⃣ Step 5: Remote Desktop and Client Authentication

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

### 6️⃣ Step 6: Group Policy & Account Lockout

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

### 7️⃣ Step 7: Disabling Accounts and Reviewing Event Logs

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
