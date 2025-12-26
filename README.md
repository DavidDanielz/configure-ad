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

<p>
  <img src="dc-setup.png" width="80%" alt="Domain Controller Setup"/>
</p>
<p>
Deployed a Windows Server 2022 virtual machine in Azure and installed the Active Directory Domain Services role.
</p>

<br />

<p>
  <img src="domain-promotion.png" width="80%" alt="Domain Promotion"/>
</p>
<p>
Promoted the server to a domain controller and created a new Active Directory domain.
</p>

<br />

<p>
  <img src="domain-join.png" width="80%" alt="Client Domain Join"/>
</p>
<p>
Joined a Windows 11 client virtual machine to the domain and verified successful authentication.
</p>

---

## 🔐 Validation & Testing

- Verified domain controller health and DNS functionality  
- Confirmed user authentication and domain login  
- Validated directory structure and domain trust  
- Ensured stable communication between domain controller and client systems  

---

## 🧠 Skills Demonstrated

- Azure Virtual Machine provisioning and networking
- Windows Server administration
- Active Directory Domain Services configuration
- Identity and access management fundamentals
- Domain authentication and client integration
- PowerShell-based system validation
- Troubleshooting domain and connectivity issues

---

## ✅ Outcome

A fully functional Active Directory environment was successfully deployed within Microsoft Azure, simulating a real-world on-premises identity infrastructure in a cloud-hosted setting. This project demonstrates practical experience with enterprise authentication systems, Windows Server administration, and cloud-based infrastructure management.

