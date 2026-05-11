<title>Windows Cert Distribution SOP | Security Portfolio</title> 

<div style="max-width: 1100px; margin: 0 auto;">

[← Back](<../>)

[View PDF Version](<./PDF Step 3 Domain CA Distribution with Auto Enrollment.pdf>)

# Purpose

This is a step-by-step procedure on distributing the Offline Root CA's certificate to all [INTERNAL-ORG] domain computers. This ensures **Windows** devices will automatically trust certificates from the Intermediate CA as well as receive auto-enrollment.

# Scope

#### In-Scope

- Group Policy Object to distribute Root CA certificate and auto enroll Windows devices

#### Out-of-Scope

- Manual install of certificates on non-domain joined or non-Windows devices

# Requirements

Permissions: Domain Administrator

Access to Windows Domain Controllers and Online Intermediate CA

Ensure Domain workstations/servers can reach the Online Intermediate CA server

# Procedure

## Creating the GPO in Domain Controller

1. Go to **Group Policy Management (gpmc.msc)**
2. **Right-click** on our **domain** and click **Create a GPO in this domain, and Link it here…**
3. Set name as necessary, for clarity I recommend **[INTERNAL-ORG]** **PKI Distribution**
4. Click **OK**

## Importing the Root CA and Intermediate CA in the GPO

1. With the named GPO created **right-click** the GPO and click **Edit…**
2. Within our GPO, go to **Computer Configuration > Policies > Windows Settings > Security Settings > Public Key Policies**
3. Right-click **Trusted Root Certification Authorities** and click **Import…**
4. In the pop-up wizard, continue until you reach **Browse…**
5. **Open** the **Root** **.cer** file that is located in the Intermediate CA server's **inetpubwwwrootCertEnroll** folder (may be done with secure media or network share)
6. Accept all wizard defaults and finish
7. Repeat steps 3-6 with the **Intermediate Certification Authorities** folder and importing the **Intermediate CA .cer file**

## Enabling Auto-Enrollment Policy in the GPO

1. Within our named GPO, go to **Computer Configuration > Policies > Windows Settings > Security Settings > Public Key Policies**
2. Under Public Key Policies, double-click **Certificate Services Client - Auto-Enrollment**
3. **Enable** the policy and **Check** the boxes for **Renew expired…** and **Update certificates…**
4. Click **Ok**

## Creation of Windows Certificate Template

**In the Intermediate CA server**

1. Open **Certification Authority (certsrv.msc)**
2. Under our server, go to **Certificate Templates**
3. Right click **Certificate Templates** and click **Manage**
4. Right-click **Workstation Authentication** and click **Duplicate Template**
5. In the **General** tab,
   1. Name the template as necessary. I suggest **[INTERNAL-ORG] Windows Authentication**
   2. Set the **Validity Period** and **Renewal Period** as necessary for workstation/server certificates (preferably **2 year** and **6 weeks** respectively)
   3. Check **Publish certificate in Active Directory**
6. In the **Compatibility** tab,
   1. Set **Certification Authority** to **Windows Server 2008 R2** (must be compatible with oldest server)
   2. Set **Certificate Recipient** to **Windows 7 / Server 2008 R2** (must be compatible with oldest server)
7. In the **Subject Name** tab,
   1. Select **Build from this Active Directory information**
   2. Change **Subject name format** to **Common name**
   3. Check the box for **DNS name**
8. In the **Extensions** tab,
   1. With **Application Policies** selected, click **Edit**
   2. In the **Edit Application Policies Extension** window**,** click **Add**
   3. In the **Add Application Policy** window, select **Server Authentication** and click **Ok**
9. In the **Security** tab,
   1. For the group **Domain Computers**, ensure the permissions, **Read**, **Enroll**, and **Autoenroll** are checked to **Allow**\
**Note**: Ensure Domain Controllers are included in Domain Computers group, otherwise DCs to receive the same permissions

## Issuing the newly created Certificate Template

1. In **Certificate Authority (certsrv.msc)**, under our server, right-click **Certificate Templates**, and click **New > Certificate Template to Issue**
2. In the **Enable Certificate Templates**, select the newly created certificate template and click **Ok**

## Verification

1. On any computer under the configured domain,
   1. Run **gpupdate /force**
   2. Run **certutil -pulse**
2. Open **Local Machine Certificate Store (certlm.msc)**
3. Check **Trusted Root Certification Authorities > Certificates** for **[INTERNAL-ORG]-ROOTCA-CA**
4. Check **Intermediate Certification Authorities > Certificates** for a **[INTERNAL-ORG]-INTERCA-CA** certificate
5. Check **Personal > Certificates** for a certificate issued by **[INTERNAL-ORG]-INTERCA-CA**.

</div>