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

Troubleshooting & Ticketing Approach (ServiceNow Mindset)

While working through this lab, I treated issues as if they were tickets coming from end users or system alerts. My approach:

Identify & categorize the issue → e.g., “App failed to install” → categorize under Software Deployment.

Gather evidence → Checked Intune logs, device sync status, and compliance reports before escalating.

Apply fixes → Adjusted detection rules, corrected policy conflicts, retried syncs.

Document resolution → Recorded root cause and fix steps (e.g., “Conflicting PIN policies; resolved by consolidating settings in Endpoint Security”).

Close ticket with knowledge base update → Treating each fix as something reusable for future incidents.

This reflects how I would approach real incidents in ServiceNow or another ITSM platform: structured, evidence-based, and documented.

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
- Office 365 Admin / Intune /Entra ID    
- Networking (DHCP, DNS, VPN, firewall rules)


Here’s a clean, copy-pasteable write-up you can drop into your repo. It captures what you built, what broke, how you proved where the break was, and how you fixed it.

---

# PXE booting Windows with WDS on Server 2022 (VirtualBox, UEFI)

## Goal

Stand up **Windows Deployment Services (WDS)** on Windows Server 2022 and UEFI-PXE boot a VirtualBox client on an **isolated lab network**.

---

## Lab Topology

* **Server**: Windows Server 2022
  Roles: AD DS, DNS, DHCP, WDS
  IP: `10.0.0.10/24` (Internal/isolated network)
* **DHCP Scope**: `10.0.0.100–10.0.0.200` (`255.255.255.0`)
* **VirtualBox network**: `Internal Network` (e.g., `LabNet`)
* **Client**: UEFI VM, NIC model **Intel PRO/1000** (E1000/E1000T)
  *Avoid paravirtualized/virtio – PXE ROM isn’t there.*

> Tip: For UEFI console sizing I set:
> `VBoxManage setextradata "Test Client" "VBoxInternal2/EfiGraphicsResolution" "1024x768"`

---

## WDS & DHCP Configuration

* WDS **Properties → DHCP**:

  * ✅ *Do not listen on DHCP ports* (because DHCP is on same box)
  * ✅ *Configure DHCP options to indicate this is also a PXE server*

* WDS **Properties → Boot**:

  * PXE policy: *Always continue the PXE boot* (for known/unknown)
  * **x64 (UEFI) default boot image**: `Boot\x64\Images\boot.wim`

* Boot programs (verified/set via `wdsutil`):

  ```cmd
  wdsutil /set-server /bootprogram:boot\x64\wdsmgfw.efi /architecture:x64uefi
  wdsutil /set-server /n12bootprogram:boot\x64\wdsmgfw.efi /architecture:x64uefi
  ```

* **DHCP options 66/67**: **Not set** (UEFI uses DHCP options + PXE/WDS logic; hard-coding 67 often breaks UEFI).

* Firewall/TFTP/ProxyDHCP listening:

  ```cmd
  netstat -an | findstr ":69"    # TFTP
  netstat -an | findstr ":4011"  # ProxyDHCP (WDS)
  ```

---

## Initial Symptoms

* Client reached **WDS Boot Manager** but looped at **“Contacting Server (10.0.0.10)”**.
* Later: **0xc0000001** immediately after contacting the server.

**Root cause**: the **UEFI boot payload** (TFTP content) was incomplete/inconsistent.

* `wdsmgfw.efi` downloadable ✔
* `boot.sdi` / `boot.wim` either missing/misplaced/not readable at first

---

## Sanity Checks (proved the path)

Enabled the Windows TFTP client and pulled the exact files the firmware needs:

```cmd
tftp -i 10.0.0.10 GET "Boot\x64\wdsmgfw.efi"
tftp -i 10.0.0.10 GET "Boot\x64\Images\boot.sdi"
tftp -i 10.0.0.10 GET "Boot\x64\Images\boot.wim"
tftp -i 10.0.0.10 GET "Boot\x64\default.bcd"
```

* Early on, `boot.sdi`/`boot.wim` failed → later fixed (see next section).
* Once all four downloaded successfully, the client proceeded.

---

## Fix: Stage the right UEFI boot files

Used the Windows ISO to source the files:

* Verified WIM contents:

  ```cmd
  dism /English /Get-WimInfo /WimFile:D:\sources\boot.wim
  ```

  Output shows:

  * Index 1: **Microsoft Windows PE (amd64)**
  * Index 2: **Microsoft Windows Setup (amd64)**

* Staged the payload where WDS serves UEFI boot:

  ```powershell
  $dst = 'C:\RemoteInstall\Boot\x64\Images'
  New-Item -ItemType Directory -Force -Path $dst | Out-Null
  Copy-Item 'C:\Windows\Boot\DVD\PCAT\boot.sdi' "$dst\boot.sdi" -Force
  Copy-Item 'D:\sources\boot.wim'              "$dst\boot.wim" -Force
  ```

> Note: Files **copied manually** into `RemoteInstall\Boot\x64\Images` are **not “registered” WDS images** (they have no WDS Image GUID). That’s fine for **default UEFI boot** (WDS just needs the files present), but you won’t see these in `Get-WdsBootImage`.

---

## WDS image store cleanup (optional)

MMC showed a default **“Microsoft Windows Setup (x64)”** boot image.
Deleting failed with **“Access is denied”** because it was the default/in use.

Two ways to handle it:

### PowerShell (when the image was *added* via WDS and has an ID)

```powershell
Import-Module WDS
Get-WdsBootImage | ft Name,Architecture,ImageGroup,ImageGuid

# If found, clear default then remove:
$img = Get-WdsBootImage | ? { $_.Name -eq 'Microsoft Windows Setup (amd64)' -and $_.Architecture -eq 'x64' }
try { Set-WdsBootImage -ImageGuid $img.ImageGuid -IsDefault:$false -ErrorAction Stop } catch {}
Remove-WdsBootImage -ImageGuid $img.ImageGuid -Force
```

### WDSUTIL equivalents

```cmd
wdsutil /Get-AllImages /ImageType:Boot
wdsutil /Get-Image /ImageType:Boot /Architecture:x64 /Image:"Microsoft Windows Setup (amd64)" /Show:All
wdsutil /Set-Image /ImageType:Boot /Architecture:x64 /Image:"Microsoft Windows Setup (amd64)" /IsDefault:No
wdsutil /Remove-Image /ImageType:Boot /Architecture:x64 /Image:"Microsoft Windows Setup (amd64)"
```

> If the image was never “added” (you only copied files), it won’t have a GUID and won’t appear in the PowerShell cmdlets. That’s expected.

If you get locked files/errors, bounce services:

```cmd
net stop wdsserver
net stop dhcpserver
net start dhcpserver
net start wdsserver
```

---

## Client Config (VirtualBox)

* **Firmware**: UEFI
* **NIC model**: Intel PRO/1000 MT Desktop (82540EM) or 82543GC
* **Network**: Attached to the same **Internal Network** name as the server (e.g., `LabNet`)
* **Promiscuous**: Deny (default)
* **PXE path**: From UEFI boot menu choose **UEFI PXEv4** on that NIC

---

## Outcome

* After staging **`wdsmgfw.efi`**, **`boot.sdi`**, **`boot.wim`**, and verifying TFTP access, the UEFI client downloaded the boot payload and reached **Windows Setup** over PXE.
* The earlier **0xc0000001** was resolved by fixing **`boot.wim`** and **`boot.sdi`** placement/availability.

---

## Commands Reference (used in this lab)

```cmd
:: Verify TFTP/ProxyDHCP
netstat -an | findstr ":69"
netstat -an | findstr ":4011"

:: Set UEFI boot programs
wdsutil /set-server /bootprogram:boot\x64\wdsmgfw.efi /architecture:x64uefi
wdsutil /set-server /n12bootprogram:boot\x64\wdsmgfw.efi /architecture:x64uefi

:: DISM inspect WIM
dism /English /Get-WimInfo /WimFile:D:\sources\boot.wim

:: TFTP sanity pulls
tftp -i 10.0.0.10 GET "Boot\x64\wdsmgfw.efi"
tftp -i 10.0.0.10 GET "Boot\x64\Images\boot.sdi"
tftp -i 10.0.0.10 GET "Boot\x64\Images\boot.wim"
tftp -i 10.0.0.10 GET "Boot\x64\default.bcd"
```

---

## Lessons Learned

* **UEFI + WDS** does **not** need DHCP 66/67; setting 67 often breaks UEFI.
* If you see **“Contacting Server…”** forever or **0xc0000001**, verify the **TFTP path** with real downloads.
* **VirtualBox NIC** must be an **E1000** variant; paravirtualized/virtio has no PXE ROM.
* Files copied into `RemoteInstall\Boot\x64\Images` are used by UEFI **even if not registered** as WDS images (no GUID).
* If a WDS boot image won’t delete, **remove the default flag** or **restart WDS** and delete by GUID.

---

### Status

✅ PXE (UEFI) boot to Windows Setup works on an isolated VirtualBox network using a single Server 2022 VM running AD DS/DNS/DHCP/WDS.

---

