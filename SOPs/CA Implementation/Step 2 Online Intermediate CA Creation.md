<title>Intermediate CA SOP | Security Portfolio</title> 

<div style="max-width: 1100px; margin: 0 auto;">

[← Back](<../>)

[View PDF Version](<./PDF Step 2 Online Intermediate CA Creation.pdf>)

# Purpose

This is a step-by-step procedure to build/configure the online Intermediate Certificate Authority and handle all certificate tasks for the [INTERAL-ORG] environment, utilizing the trust established by the offline Root CA

In layman terms, the online Intermediate CA will represent the offline Root CA and conduct any certificate-related tasks for our environment.

# Scope

#### In-Scope

- Installation of AD CS role as an Enterprise Subordinate

- Configuration of standardized Certification encryption for use by Windows and Non-Windows devices

- Exporting Certification Signing Request from Intermediate CA server

#### Out-of-Scope

- Joining the server onto [INTERNAL-ORG] domain

# Requirements

Permissions: Domain/Local Adminstrator

Dedicated hardware/VM for Windows Server that will be online for certification

Software: Windows Server 2019 or higher

System must a FQDN (preferably INTERCA.[INTERNAL-ORG].local)

**System must be joined to the domain**

# Procedure

## Intermediate CA Role Installation

1. Open **Server Manager -> Manage -> Add Roles and Features**
2. In the **Add Roles and Features Wizard**, accept defaults until **Server Roles**
3. **In Server Roles,** add the role **Active Directory Certificate Services** and click **Add Features**
4. Accept all defaults and until **AD CS -> Role Services**
5. In **Role Services**, in addition to **Certificate Authority**, **check Certification Authority Web Enrollment** and **click Add Features**
6. Accept all defaults and until **Web Server Roles (IIS) -> Role Services**
7. In **Role Services**, check **Basic Authentication** under **Web Sever -> Security**
8. Click Next, Confirm configurations, and Install.
9. Once the installation is finished, on progress screen, click **Configure Active Directory Certificate Service on the destination server**



## Post-Installation Role Configuration

1. Ensure the **Credentials** are for the **Domain Administrator account** (following is an example)
2. For **Role Services**, check **Certification Authority** and **Certification Authority Web Enrollment**
3. For **Setup Type**, select **Enterprise CA**
4. For **CA Type**, select **Subordinate CA**
5. For **Private Key**, select **Create a new private key**
6. For **Cryptography**, select the key length **(needs to be compatible with all [INTERNAL-ORG]** **devices: Windows and Non-Windows /// Most likely 2048-bit)**
7. For **CA Name**, set the Common name as **[INTERNAL-ORG]-INTERCA-CA** (can be changed for better distinction but must match RootCA signing)
8. For **Certificate Request**, select **Save a certificate request to file on the target machine** and leave **File name** as default. (This option requires us to manually transfer the cert request from the Intermediate CA server to the Root CA server)
9. For **Certificate Database**, leave as default
10. Confirm configuration and hit **Configure**

## Root CA Cert Installation

1. Mount the secure media containing the root certificate file (**.cer**) and the root revocation list file (**.crl**)
2. Transfer the root files to the Intermediate CA Server's file path **C:WindowsSystem32CertSrvCertEnroll**
3. **Right-click** the **.cer** file and select **Install Certificate**
4. In **Certificate Import Wizard**, set the Store Location as **Local Machine**
5. In the UAC popup, allow **Rundll32** to make changes
6. Select **Place all certificates in the following store** and click **Browse…**
7. Set the Certificate Store as **Trusted Root Certification Authorities**
8. Click **Ok**, Click **Next**, Click **Finish.**
9. Repeat steps 3-8 for the **.crl** file (store in intermediate cert auth)

## CertEnroll Folder Creation For Environment Certifications & IIS Config

1. In File Explorer, go to file path **C:inetpubwwwroot**
2. Create a new folder **CertEnroll**
   1. Copy over the root certificate file (.cer) and the root revocation list file (.crl) from C:WindowsSystem32CertSrvCertEnroll
   2. Ensure the CertEnroll folder has Read access for the IUSR account or Everyone
3. In IIS Manager for our server
   1. **Right-click** the **CertEnroll** folder and select **Convert to Application**
   2. **Ensure .crl** and **.cer** are registered **MIME** types
      1. Right-click server name and open **MIME Types**
      2. If no **application/x-x509-ca-cert** entry, add it manually
      3. If no **application/pkix-crl** entry, add it manually
   3. Go to **Request Filtering > Edit Feature** Settings, and check **Allow Double Escaping**

## Inbound Firewall Rule to allow PKI Traffic

1. Open **Windows Defender Firewall with Advanced Security**
2. **Right-Click Inbound Rule** and select **New Rule…**
3. For **Rule Type**, set as **Port**
4. Set the **Specific local port** as **80**
5. Accept all defaults and click next
6. Set the **Rule Name** as **IIS Web Server**

## Exporting Intermediate CA Server's Certification Request for Root CA Signing

1. Locate the **.req** file (should be in **C:** directory)
2. Transfer the **.req** file into secure media and mount the media onto the Root CA server
3. Refer to SOP for signing the Intermediate CA Server's Certification Request on the Root CA Server

## Importing Signed Intermediate CA Server's Certification Request

1. Mount the secure media containing the signed **.cer** file
2. Open **Certificate Authority (certsrv.msc)**
3. **Right-click** our server, go to **All Tasks**, and click on **Install CA Certificate…**
4. In the **Select file to complete CA installation** pop-up, go to secure media and **Open** the **InterCA.cer** file
   1. You may need to change the file type filter from *.p7b
   2. **Ensure .cer file is also in the C:inetpubwwwrootCertEnroll folder**
   3. Ensure a copy of the intermediate CRL exists in the same folder as well (may have to publish the CRL to move)

## Verification of Successful Root CA Certification Implementation

1. In **Certificate Authority (certsrv.msc)**, **Right-click** our server, go to **All Tasks**, and click **Start Service**

Green checkmark next to the server name is a good sign

2. For further review of our successful certificate, **right-click** the server and click **Properties**
3. In the **Properties** dialog box, click **View Certificate** for the only available CA Certificate
4. You may review the **Certificate** information to confirm all fields are correct

## Clearing any old Certificate Data on INTERCA

If server has any existing certificate data that will be overwritten

1. Run **certlm.msc** (Local Machine Store)
   1. Delete old root from Trusted Root Certification Authorities
   2. Delete old intermediate from Intermediate Certification Authorities
2. Purge the AIA/CDP URL cache
   1. Run as admin: **certutil -urlcache * delete**
3. Clean Up Web Folder
   1. Delete all files in **C:inetpubwwwrootCertEnroll**

## Verifying IIS connection

1. Ensure both **.cer** and **.crl** files are in **C:inetpubwwwrootCertEnroll**
2. On a different machine connected to [INTERNAL-ORG], go to URLs:
   1. http://[IIS-Server-Name-Or-FQDN]/CertEnroll/[Cerfile].cer
   2. http://[IIS-Server-Name-Or-FQDN]/CertEnroll/[Crlfile].crl
3. Successful connectivity if both results in opening/downloading file

## Verifying IIS validity

1. Ensure both **.cer** and **.crl** files are in **C:inetpubwwwrootCertEnroll**
2. Open command prompt
   1. **certutil -url http://&lt;Your-IIS-Server&gt;/CertEnroll/&lt;Your-INTERCA-Name&gt;.crl**
3. In tool window, select **URL** radio button and click **Retrieve**
4. Successful validity if row is green and reports **Verified**

Next step is to create a Certificate Template on the Domain Controller. There is a SOP for this.

</div>