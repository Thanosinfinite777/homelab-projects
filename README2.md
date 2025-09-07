Active Directory’s plain LDAP (TCP/389) is unencrypted. Enabling LDAPS (TCP/636) encrypts directory traffic using TLS and a certificate bound to your domain controller (DC). This improves security for service binds, applications, and management tools.

Lab Topology (example)

Domain: lab.local

DC / CA host: WIN-SEC6ES0S15U.lab.local (Windows Server)

Roles: AD DS, DNS, AD CS (Enterprise Root CA)

Root CA name: LAB-RootCA

In a lab it’s fine to run the CA on the DC. In production, use a separate, dedicated CA.

What you built (high level)

Installed Active Directory Certificate Services (AD CS) and configured an Enterprise Root CA.

Ensured a DC certificate with Server Authentication EKU and the DC’s FQDN in SAN was issued to the DC.

Enabled auto-enrollment via Group Policy so domain members automatically get certs.

Verified LDAPS is listening and serving the new certificate on TCP/636.

Step-by-step
1) Baseline checks

Confirm LDAP (389) works and whether LDAPS (636) is already listening:

Test-NetConnection WIN-SEC6ES0S15U.lab.local -Port 389
Test-NetConnection WIN-SEC6ES0S15U.lab.local -Port 636


Before configuration you’ll see port 389 = True, 636 = False.

2) Install the AD CS role (Certification Authority)
Install-WindowsFeature ADCS-Cert-Authority -IncludeManagementTools

3) Configure an Enterprise Root CA
Install-AdcsCertificationAuthority `
  -CAType EnterpriseRootCA `
  -CACommonName "LAB-RootCA" `
  -KeyLength 2048 `
  -HashAlgorithmName SHA256 `
  -ValidityPeriod Years `
  -ValidityPeriodUnits 10


Result: you now have an issuing CA that can publish domain certs.

4) Publish/verify the right certificate template(s)

Open Certification Authority (certsrv.msc) → your CA → Certificate Templates.

For LDAPS, your DC needs a cert with:

Enhanced Key Usage (EKU): Server Authentication (1.3.6.1.5.5.7.3.1)

Subject Alternative Name (SAN): DNS = WIN-SEC6ES0S15U.lab.local

Private key present

Templates that satisfy this in most domains:

Domain Controller Authentication

(Or Domain Controller—depends on domain defaults)

If Domain Controller Authentication isn’t listed under your CA:

Right-click Certificate Templates → New → Certificate Template to Issue → choose Domain Controller Authentication.

5) Enable auto-enrollment (GPO)

Open Group Policy Management (gpmc.msc) → Edit a GPO applied to your DCs (often Default Domain Policy in labs):

Computer Configuration → Policies → Windows Settings →
Security Settings → Public Key Policies →
Certificate Services Client – Auto-Enrollment → Enabled

✔️ Renew expired certificates

✔️ Update certificates that use certificate templates

Force policy refresh:

gpupdate /force
certutil -pulse


If the DC still doesn’t enroll, restart the DC or the “Active Directory Domain Services” service so LDAP picks up new certificates.

6) Verify the DC has the correct certificate

Open Certificates (Local Computer) (certlm.msc) → Personal → Certificates.
You should see a cert:

Issued to the DC’s FQDN

Issued by LAB-RootCA

Intended Purposes includes Proves your identity to a remote computer (Server Auth)

Shows “You have a private key that corresponds to this certificate.”

PowerShell quick check:

Get-ChildItem Cert:\LocalMachine\My |
  Where-Object { $_.EnhancedKeyUsageList.FriendlyName -contains 'Server Authentication' } |
  Select Subject, NotAfter, Thumbprint

7) Confirm LDAPS is working
Test-NetConnection WIN-SEC6ES0S15U.lab.local -Port 636 -InformationLevel Detailed


GUI test (built-in):

Run ldp.exe

Connection ▸ Connect…

Server: WIN-SEC6ES0S15U.lab.local, Port: 636, check SSL, click OK
You should see a successful bind and the presented cert chain.

What success looks like

A DC certificate is present (Local Computer → Personal) with Server Authentication EKU & private key.

Port 636 returns TcpTestSucceeded: True.

ldp.exe connects with SSL on 636 and shows your LAB-RootCA issued cert.

Your clients trust the LAB-RootCA (by default in-domain machines do).

Troubleshooting tips

No cert on DC: Template not published, GPO not applied, permissions on template, or CA not reachable.

SAN missing FQDN: App won’t trust the server name → fix template or reissue.

Port 636 closed: Restart DC or AD DS service after enrollment so LDAP reloads the cert.

Chain not trusted on clients: Ensure your root CA cert is in Trusted Root Certification Authorities on clients.

Security notes (production)

Use a dedicated issuing CA, not the DC. Keep keys protected.

Publish reachable CRL/AIA locations.

Rotate keys & enforce strong crypto (SHA-256+, 2048-bit+).

Monitor certificate enrollment and expiry.
