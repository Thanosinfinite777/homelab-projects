Active Directory + Kerberos + IIS (HTTPS) Lab

A minimal Windows lab that demonstrates Kerberos authentication to a web server over HTTPS using your own Enterprise CA.

Topology

DC1 (Domain Controller, DNS, KDC, Enterprise Root CA)

Hostname: WIN-SEC6ESOS15U (domain-joined, forest root)

AD DS, DNS, AD CS (Enterprise Root CA)

IP: 10.0.0.10

WEB1 (IIS web server, domain member)

Hostname: WEB1

IP: 10.0.0.20

Domain: lab.local
Network: 10.0.0.0/24

Kerberos requires the client, KDC, and service to agree on time (skew ≤ 5 minutes), DNS (A/SPN records), and SPNs that match the host or URL used.

1) VirtualBox Networking

We used one internal lab network so the VMs can talk to each other but stay isolated:

VirtualBox Internal Network named LabNet

DC1: Adapter 1 → Internal Network LabNet

WEB1: Adapter 1 → Internal Network LabNet

Optional: add a 2nd adapter (NAT) to a VM if you want Internet, but keep Kerberos flows on LabNet.

2) IP & DNS

On each VM, set a static IPv4 address and point DNS to the DC:

DC1

IP: 10.0.0.10, Mask: 255.255.255.0

DNS: 127.0.0.1 (or 10.0.0.10)

WEB1

IP: 10.0.0.20, Mask: 255.255.255.0

DNS: 10.0.0.10 (the DC)

Verify from WEB1:

Resolve-DnsName win-sec6esos15u.lab.local
Resolve-DnsName web1.lab.local
Test-NetConnection 10.0.0.10 -Port 53   # DNS


If you get “DNS name does not exist”, add an A record in DNS Manager on DC1:

Zone: lab.local

A record: web1 → 10.0.0.20

(Optional but nice) Reverse zone 10.0.0.x and a PTR for 10.0.0.20.

3) Domain Join

On WEB1:

Rename to WEB1 (Server Manager → Local Server → Computer name → Change)

Join to domain lab.local

Reboot

Verify:

whoami /fqdn         # should show .../WEB1$
nltest /dsgetdc:lab.local

4) Install Roles

On DC1 (already your DC/DNS):

AD CS (Enterprise Root CA)
We installed Enterprise Root CA with SHA256, 2048 key.

On WEB1:

Install-WindowsFeature Web-Server -IncludeManagementTools


Enable RDP if you want to connect graphically:

Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name fDenyTSConnections -Value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"

5) Create the Web Server Certificate Template

On DC1 (CA server):

Open Certificate Templates Console (certtmpl.msc)

Right-click Web Server → Duplicate Template

Name it Copy of Web Server

Tabs to set:

Compatibility: CA = 2012, Recipient = 2012 (fine for 2012–2022)

Subject Name: Build from Active Directory information
Check DNS name, User principal name (UPN), Service principal name (SPN) if available

Security: Add WEB1 and grant:

Enroll

Autoenroll (optional)

(Leave other defaults; key usage already includes Digital Signature/Key Encipherment)

Close the template.

Publish the template on the CA:

Open Certification Authority (certsrv.msc)

Right-click Certificate Templates → New → Certificate Template to Issue

Select Copy of Web Server

If you change template permissions later, you can bounce the CA:
net stop certsvc && net start certsvc

6) Set Service Principal Names (SPNs)

Kerberos must map an HTTP SPN to the WEB1 computer account so tickets can be issued.

On DC1 (or any admin PowerShell):

# Use -S to avoid duplicates (it fails if the SPN already exists)
setspn -S HTTP/web1        LAB\WEB1$
setspn -S HTTP/web1.lab.local LAB\WEB1$

# Verify
setspn -Q HTTP/web1*


If you ever get “Duplicate SPN found,” it means that SPN was already registered (possibly to another account). Query with setspn -Q and remove from the wrong principal if necessary.

7) Request the Web Server Certificate on WEB1

On WEB1:

Win + R → certlm.msc

Personal → Certificates → All Tasks → Request New Certificate

Pick Active Directory Enrollment Policy

Check Copy of Web Server → Enroll

You should get STATUS: Succeeded ✅

Confirm it’s in Personal\Certificates and the Intended Purposes shows Server Authentication.

8) Bind HTTPS in IIS

On WEB1:

Open IIS Manager → Default Web Site → Bindings (right pane)

Add… → Type: https, Port 443, SSL certificate: Copy of Web Server (the cert you just enrolled)

OK → Restart the Default Web Site

Test from WEB1 (or any domain machine that trusts your CA):

Test-NetConnection web1.lab.local -Port 443
Start-Process "https://web1.lab.local"


You should see the IIS welcome page over HTTPS without browser trust warnings (because your CA is trusted domain-wide).

9) Kerberos Validation

Kerberos pre-reqs checklist:

Time OK?
w32tm /resync on both VMs (or check their clocks are within 5 minutes)

DNS OK?
Resolve-DnsName web1.lab.local

SPNs registered?
setspn -Q HTTP/web1*

Service ticket check (run on the client after browsing to the site):

klist purge            # (optional) clear existing tickets)
Start-Process "https://web1.lab.local"
klist                  # expect krbtgt ticket + a ticket for HTTP/web1.lab.local


In your screenshot you saw krbtgt and a host/DC ticket. After hitting the HTTPS site with Integrated Windows Authentication (IWA), you should see a service ticket for HTTP/web1.lab.local.
If you don’t, check:

IE/Edge Local Intranet zone (IWA allowed), or use inetcpl.cpl → Security → Local intranet → Sites → Advanced → add https://web1.lab.local

That you visited the site as https://web1.lab.local
 (not by IP)

SPN is on WEB1$ (not on a user or the wrong computer)

10) Useful Diagnostic Commands (what we used)

Networking/DNS

ipconfig /all
Resolve-DnsName web1.lab.local
Resolve-DnsName win-sec6esos15u.lab.local
Test-NetConnection 10.0.0.10 -Port 53
Test-NetConnection web1.lab.local -Port 443


Firewall & RDP

Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
Enable-NetFirewallRule -DisplayGroup "World Wide Web Services (HTTP)"
Enable-NetFirewallRule -DisplayGroup "Secure World Wide Web Services (HTTPS)"


Kerberos / KDC / SPN

klist
klist purge
setspn -Q HTTP/web1*
setspn -S HTTP/web1        LAB\WEB1$
setspn -S HTTP/web1.lab.local LAB\WEB1$


CA / Certificates

# Restart CA service after template changes (on DC1)
net stop certsvc && net start certsvc

# On DC1: quick view of which templates are configured for auto-enroll (not required, but we used it for visibility)
certutil -catemplates

11) Why earlier things failed (and how we fixed them)

Wrong path (WDS / boot.wim): We initially spent time in WDS/image tooling—that’s unrelated to Kerberos/IIS. The fix was to focus on AD DS + DNS + AD CS + IIS only.

“DNS name does not exist”: WEB1 couldn’t resolve dc.lab.local because the A record didn’t exist (and PTR zone was missing too). We created A (and PTR) records so web1.lab.local and win-sec6esos15u.lab.local resolve.

SPN duplicate / wrong principal: setspn -S protects you by refusing to create duplicates. Query with setspn -Q and ensure HTTP SPNs are on LAB\WEB1$ (the machine account), not a user.

No HTTPS cert available: WEB1 couldn’t see “Copy of Web Server” until we:

Duplicated Web Server template

Granted WEB1 Enroll/Autoenroll rights

Published the template on the CA

Requested the cert on WEB1

Kerberos ticket not visible: You’ll only see the HTTP/web1.lab.local ticket after hitting the site with IE/Edge (IWA) using the FQDN. Clear with klist purge, browse the site, run klist again.

12) What you should be able to demo now

Browse to https://web1.lab.local and load the IIS landing page with a valid chain to LAB-RootCA.

Show Kerberos in action:

klist → see your TGT (krbtgt) and a service ticket for HTTP/web1.lab.local.

Prove SPNs and DNS:

setspn -Q HTTP/web1* shows the SPNs on LAB\WEB1$

Resolve-DnsName web1.lab.local → 10.0.0.20
