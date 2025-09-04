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
- 
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

  Microsoft Intune & Entra ID Lab

This project demonstrates end-to-end modern endpoint management and security using Microsoft Intune and Entra ID in a Windows 11 virtual lab. The goal was to simulate real enterprise scenarios: packaging and deploying apps, enforcing compliance, managing updates, and securing access with Conditional Access.

🔹 Lab Highlights

App Deployment → Packaged VLC Media Player with the Win32 Content Prep Tool, deployed via Intune, and validated with detection rules.

Configuration Policies → Enforced Windows Hello PIN requirements (length, complexity, history).

Update Management → Created a Pilot Update Ring deferring feature updates by 7 days, quality updates by 2 days.

Endpoint Security → Configured Microsoft Defender Antivirus (real-time + cloud protection, scheduled scans) and Defender Firewall (enabled for all profiles).

Conditional Access → Built a policy requiring devices to be marked compliant before accessing Microsoft 365 apps.

Reporting & Monitoring → Tracked compliance, app deployment status, and update ring assignments in the Intune Admin Center.

🔹 Challenges & Learning Process

VirtualBox limitations → BitLocker was skipped since encryption can cause boot issues in VMs. Learned when to adapt lab design to the environment.

Policy conflicts → Encountered PIN length conflicts between Endpoint Security and Configuration Profiles. Learned to consolidate settings and avoid overlap.

App packaging issues → Needed to use .\IntuneWinAppUtil.exe correctly in Command Prompt, and confirm silent install commands for detection.

Conditional Access setup → Had to disable Security Defaults in Entra to enable custom CA policies — discovered how defaults vs. CA policies differ.

Sync delays → Learned Intune reporting isn’t instant; forced device syncs and waited for compliance/app install state updates.

Each issue deepened understanding of how Intune and Entra ID behave in real-world environments.

🔹 Next Steps

Add BitLocker encryption testing outside VM environment.

Package additional Win32/MSI apps (7-Zip, Notepad++).

Expand Conditional Access with MFA and location-based rules.

Explore automation with PowerShell + Graph API for bulk deployments.

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


## Skills Practiced
- Windows Server administration  
- Domain join / Group Policy  
- Office 365 / Intune  
- Ticketing systems (ServiceNow, Jira, Zendesk)  
- Networking (DHCP, DNS, VPN, firewall rules)  
