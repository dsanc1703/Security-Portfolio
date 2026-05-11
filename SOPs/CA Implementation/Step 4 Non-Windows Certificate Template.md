<title>Non-Windows Cert Templates SOP | Security Portfolio</title> 

<div style="max-width: 1100px; margin: 0 auto;">

[← Back](<../../>)

[View PDF Version](<./PDF Step 4 Non-Windows Certificate Template.pdf>)

# Purpose

Step-by-step procedure on creating Certificate Templates that will be compatible with our non-Windows devices of varying capabilities.

# Scope

#### In-Scope

- Creation of Certificate Template with 2048-bit key and SHA 256 for Non-Windows devices
- Creation of Certificate Template with 2048-bit key and SHA 1 for Non-Windows devices
- Creation of Certificate Template with 1024-bit key and SHA 1 for Non-Windows devices

#### Out-of-Scope

- Manual install of certificates on non-domain joined or non-Windows devices

# Requirements

- Permissions
  - Domain Administrator on Intermediate CA
- Remote Access to Intermediate CA server

# Procedure

## \[High-Security\] Creation of SHA256-2048bit Non-Windows Certificate Template

**In the Intermediate CA server**

1. Open **Certification Authority (certsrv.msc)**
2. Under our server, go to **Certificate Templates**
3. Right click **Certificate Templates** and click **Manage**
4. Right-click **Web Server** and click **Duplicate Template**
5. In the **General** tab,
   1. Name the template as necessary. I suggest **SHA256-2048 NonWindows Authentication**
   2. Set the **Validity Period** as necessary (preferably **2-3 years**)\
Some web browsers might reject if validity is longer than 398 days, if so change the Validity Period to 1 year
6. In the **Compatibility** tab,
   1. Set **Certification Authority** as **Windows Server 2008 R2**
   2. Set **Certification Recipient** as **Windows 7 / Server 2008 R2**
7. In the **Subject Name** tab,
   1. Select **Supply in the request**
8. In the **Cryptography** tab,
   1. Set **Provider Category** as **Legacy Cryptographic Service Provider**
   2. Set **Algorithm Name** as **RSA**
   3. Set **Minimum Key Size** as **2048**
   4. Check **Requests must use one of the following providers:**
   5. For **Providers**, check **Microsoft RSA Schannel Cryptographic Provider**
   6. For **Request hash**, set to **SHA256**
9. In the **Extension** tab,
   1. For **Application Policies**, ensure **Server Authentication** is listed
   2. For **Key Usage**, ensure **Digital Signature** and **Key Encipherment** are listed
10. In the **Request Handling** tab,
    1. Set **Purpose** to **Signature and encryption**
    2. Uncheck **Allow private key to be exported** (for security)
11. In the **Security** tab,
    1. For any group, such as Domain Admins, that will be allowed to enroll/issue certs on InterCA, ensure the permissions, **Read** and **Enroll** are checked to **Allow**

## \[Med-Security\] Creation of SHA1-2048bit Non-Windows Certificate Template

**In the Intermediate CA server**

1. Open **Certification Authority (certsrv.msc)**
2. Under our server, go to **Certificate Templates**
3. Right click **Certificate Templates** and click **Manage**
4. Right-click **Web Server** and click **Duplicate Template**
5. In the **General** tab,
   1. Name the template as necessary. I suggest **SHA1-2048 NonWindows Authentication**
   2. Set the **Validity Period** as necessary (preferably **2-3 years**)\
Some web browsers might reject if validity is longer than 398 days, if so change the Validity Period to 1 year
6. In the **Compatibility** tab,
   1. Set **Certification Authority** as **Windows Server 2008 R2**
   2. Set **Certification Recipient** as **Windows 7 / Server 2008 R2**
7. In the **Subject Name** tab,
   1. Select **Supply in the request**
8. In the **Cryptography** tab,
   1. Set **Provider Category** as **Legacy Cryptographic Service Provider**
   2. Set **Algorithm Name** as **RSA**
   3. Set **Minimum Key Size** as **2048**
   4. Check **Requests must use one of the following providers:**
   5. For **Providers**, check **Microsoft RSA Schannel Cryptographic Provider**
   6. For **Request hash**, set to **SHA1**
9. In the **Extension** tab,
   1. For **Application Policies**, ensure **Server Authentication** is listed
   2. For **Key Usage**, ensure **Digital Signature** and **Key Encipherment** are listed
10. In the **Request Handling** tab,
    1. Set **Purpose** to **Signature and encryption**
    2. Uncheck **Allow private key to be exported** (for security)
11. In the **Security** tab,
    1. For any group, such as Domain Admins, that will be allowed to enroll/issue certs on InterCA, ensure the permissions, **Read** and **Enroll** are checked to **Allow**

## \[Low-Security\] Creation of SHA1-1024bit Non-Windows Certificate Template

**In the Intermediate CA server**

1. Open **Certification Authority (certsrv.msc)**
2. Under our server, go to **Certificate Templates**
3. Right click **Certificate Templates** and click **Manage**
4. Right-click **Web Server** and click **Duplicate Template**
5. In the **General** tab,
   1. Name the template as necessary. I suggest **SHA1-1024 NonWindows Authentication**
   2. Set the **Validity Period** as necessary (preferably **2-3 years**) \
Some web browsers might alert if validity is longer than 398 days, if so change the Validity Period to 1 year
6. In the **Compatibility** tab,
   1. Set **Certification Authority** as **Windows Server 2003**
   2. Set **Certification Recipient** as **Windows XP / Server 2003**
7. In the **Subject Name** tab,
   1. Select **Supply in the request**
8. In the **Cryptography** tab,
   1. Set **Provider Category** as **Legacy Cryptographic Service Provider**
   2. Set **Algorithm Name** as **RSA**
   3. Set **Minimum Key Size** as **1024**
   4. Check **Requests must use one of the following providers:**
   5. For **Providers**, check **Microsoft Strong Cryptographic Provider**
   6. For **Request hash**, set to **SHA1**
9. In the **Extension** tab,
   1. For **Application Policies**, ensure **Server Authentication** is listed
   2. For **Key Usage**, ensure **Digital Signature** and **Key Encipherment** are listed
10. In the **Request Handling** tab,
    1. Set **Purpose** to **Signature and encryption**
    2. Uncheck **Allow private key to be exported** (for security)
11. In the **Security** tab,
    1. For any group, such as Domain Admins, that will be allowed to enroll/issue certs on InterCA, ensure the permissions, **Read** and **Enroll** are checked to **Allow**

## Issuing the newly created Certificate Template

**This step needs to be done for each of the three templates created**

1. In **Certificate Authority (certsrv.msc)**, under our server, right-click **Certificate Templates**, and click **New > Certificate Template to Issue**
2. In the **Enable Certificate Templates**, select the created certificate template and click **Ok**

</div>