# homelab-projects
My personal IT home lab projects: Active Directory, Group Policy, Intune, etc

# Brandon’s IT Home Lab

This repo documents my IT lab projects as I work toward becoming a System Administrator.

## Projects
- **Active Directory Domain Setup**  
  Built a Windows Server 2022 domain controller, added DNS & DHCP, and joined Windows 10 clients.
  ## 🛠️ Active Directory Domain Setup

  🔹 Lab Setup
- **Host Machine**: Windows 11 Pro, 16 GB RAM, Intel i7-12700H  
- **External Storage**: Crucial X10 1TB Portable SSD  
- **Virtualization**: VirtualBox 7.x  

 Virtual Machines
- **DC01** – Windows Server 2022 (Domain Controller)  
- **Win10-Client01** – Windows 10 Pro (domain-joined client)  



 🔹 What I’ve Built So Far
- Installed **Windows Server 2022** on DC01  
- Promoted server to **Domain Controller** for domain `lab.local`  
- Configured **static IP addressing** (10.0.0.10 for DC, 10.0.0.101 for client)  
- Installed and configured **DNS** (forwarders + internal records)  
- Cleaned up DNS zone to fix resolution issues  
- Added **DeptDocs shared folder** with NTFS + Share permissions  
- Joined **Windows 10 client** to the domain  
- Confirmed **AD user logon** (`lab\TestUser`) from client  
- Enabled **Group Policy Management** for domain users  
- Fixed **networking** with dual NICs (Internal Network for lab + NAT for Interne
- Created a network share with ntfs permissions

## 🔹 Troubleshooting Wins
- Solved DNS forwarder problems (Google 8.8.8.8, Cloudflare 1.1.1.1)  
- Cleaned up NAT adapter records polluting DNS zone  
- Fixed issue where `\\dc01\DeptDocs` didn’t work by aligning DNS records  
- Learned difference between A record vs CNAME for hostname resolution  

What I Learned
- How AD DS centralizes authentication and management
- How DNS integrates with Active Directory for name resolution
- How DHCP scopes automate client IP addressing
- Troubleshooting common issues (DNS misconfig, DHCP scope errors, client join failures)
- Hands-on use of Server Manager and Active Directory Users & Computers (ADUC)
- Group Policy (GPO)  
- File and Folder Permissions (NTFS + Share)  
- Windows networking (Internal vs NAT adapters in VirtualBox)  
- Troubleshooting DNS + SMB issues



- **Group Policy Testing**  
  Created GPOs to manage BitLocker, enforce password policies, and control user environments.  

- **Microsoft 365 + Entra ID (Azure AD)**  
  Experimented with hybrid join, Intune enrollment, and Microsoft 365 admin settings.
  Tenant & Intune

Created a brand-new Microsoft Entra ID (Azure AD) tenant.

Activated a Microsoft Intune Plan 1 Trial subscription (25 seats).

Verified the subscription in the Microsoft 365 Admin Center (Billing → Your products).

User Setup

Created / used a test Azure AD user.

Assigned an Intune license to that user in the Microsoft 365 Admin Center (Licenses & apps).

MDM / Enrollment Configuration

In Intune admin center → Devices → Enrollment → Windows enrollment → Automatic Enrollment:

Set MDM user scope = All (so any licensed user’s device auto-enrolls).

In Entra admin center → Devices → Device settings:

Confirmed that users are allowed to join devices to Azure AD.

Reset Secure Boot keys to allow Win11 to pass requirements.

Windows 11 Virtual Machine

Installed VirtualBox 7.x.

Created a dedicated VM storage folder: D:\IT-Lab\VMs.

Downloaded official Windows 11 ISO (5.15 GB) via Microsoft’s MCT/download page.

Created new VM in VirtualBox with:

Windows 11 Pro (64-bit)

8 GB RAM (6144 MB)

2 CPUs (you can safely bump to 4 since you have 20 logical cores)

Chipset = ICH9

EFI + Secure Boot enabled

TPM 2.0 added

Video Memory = 128 MB

Graphics Controller = VBoxSVGA

SATA Controller (AHCI): Port 0 = Win11-Intune.vdi (64 GB), Port 1 = Optical Drive → Windows11.iso

Use Host I/O Cache = On

Boot order: Optical → Hard Disk

Windows 11 OOBE

Completed installer and OOBE screens step by step:

Set region, keyboard, skip second keyboard.

Chose Set up for work or school.

Signed in with Azure AD user (licensed).

Approved Microsoft Authenticator MFA.

Created a PIN that meets complexity requirements.

VM successfully booted to desktop as an Entra ID joined device.  

- **Splunk Home Lab**  
  Installed Splunk trial to collect and analyze logs from Windows and Linux test machines.  

## Skills Practiced
- Windows Server administration  
- Domain join / Group Policy  
- Office 365 / Intune  
- Ticketing systems (ServiceNow, Jira, Zendesk)  
- Networking (DHCP, DNS, VPN, firewall rules)  

---
Save the README → now your GitHub repo looks professional, even with just text.
