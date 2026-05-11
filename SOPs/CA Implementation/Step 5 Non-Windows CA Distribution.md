<title>Non-Windows Cert Distribution SOP | Security Portfolio</title> 

<div style="max-width: 1100px; margin: 0 auto;">

[← Back](<../../>)

[View PDF Version](<./PDF Step 5 Non-Windows CA Distribution.pdf>)

# Purpose

"Standardized" step-by-step procedure for requesting, signing, and installing certificates on non-windows devices using an Active Directory Certificate Services (AD CS) environment

# Scope

#### In-Scope

- Generation of Certificate Signing Request (CSR) on third-party hardware
- Signing CSR with the Intermediate CA
- Importing signed Certificate back to third-party hardware

#### Out-of-Scope

- Steps specific to any vendor-specific hardware

# Requirements

- Permissions
  - Access to \[Intermediate CA Server\] user account with **Read** and **Enroll** permissions for templates in Certificate Authority
  - Privileged account on non-windows allowed to configure "Security" settings
- Files: **.cer/.crt** for both Offline Root and Online Intermediate CA (if upload is possible)

# Procedure

## Importing Certificate Authority certificates into Non-Windows Device

**Must be done before importing any signed CSR**

1. With **privileged account**, log into the (web) management interface of target device
2. Navigate to **Security** or **Certificate** section of interface
3. **If available**, begin target device's process to **configure/set-up/import** a **CA Certificate**
4. When importing,
   1. **If only one CA Certificate can be imported**, import the Intermediate CA certificate
   2. **If more than one CA Certificate can be imported**
      1. **First** import the Root CA certificate
      2. **Second** import the Intermediate CA certificate

Certificate Authority certificates (**.crt/.cer**) can be found in **\\\\\[Intermediate CA Server\]\\CertEnroll\\**

## Generating the Certificate Signing Request on Non-Windows Device

1. With **privileged account**, log into the (web) management interface of target device
2. Navigate to **Security** or **Certificate** section of interface
3. **If possible,** consider changing the **Hash Algorithm** and the **Key Length** to fit our strongest certificate template (**SHA256 / 2048-bits**)
   1. Upgrading **Hash Algorithm** from SHA1 to SHA256 might bring a slight increase in time for a TLS handshake
   2. Upgrading **Key Length** from 1024 to 2048 bits might bring initial connection "lag" due to 8x computational expense
4. Select the option to **configure/set-up** the device's certificate
   1. If provided with more options, select **Create Certificate Request** or **Create Certificate Signing Request**
5. When providing additional information for the request,
   1. Ensure **Common Name** and **Subject Alternative Name** is the device's **fully qualified domain name (FQDN)**
   2. **All other metadata is ignored** by our CAs but you may set them as necessary
   3. This may be where you can change the hash and key length
   4. Click **Continue** or **Next**
6. After the certificate signing request has been created, save and rename the **.csr** file as necessary

## Signing Certificate Signing Request Methods

### Signing the .csr with the Intermediate CA Server's Web Enrollment Portal

**The .csr file must be base-64 encoded**

1. On a browser, go to [http://\[Intermediate_Server_FQDN\]/certsrv/](http://interca.scada.local/certsrv/)
2. Authenticate as a user with **Enroll** permissions set for the certificate template to be used
3. Go to **Request a certificate >** **advanced certificate request**
4. For **Saved Request,** paste the content of our targeted **.csr** file
5. For **Certificate Template,** select the certificate template that is compatible with the **.csr** file
6. **If unable to set Subject Alternative Name during .csr creation**
   1. For **Additional Attributes**, enter **san:dns=**FQDN**&ipaddress=**IP
7. Click **Submit**

### Signing the .csr through CLI on remote computer

**Must be a domain user that has Enroll permissions on template and allowed RPC to Intermediate CA server**

1. Open command prompt (cmd.exe)
2. Navigate (**cd**) to directory containing the targeted **.csr** file
3. **Run** (with adjusted template name)
   1. **If unable to set Subject Alternative Name during .csr creation**, add to the following command:
      1. **\-attrib "SAN:dns=**FQDN&ipaddress=IP**"**
   2. **certreq -submit -attrib "CertificateTemplate:**TemplateName**"** fileexample.csr
4. In **Certification Authority List** window, select **\[Intermediate CA Name\]**
5. If submission successful, save and rename the **.cer/.crt** file as necessary

## Importing Signed Certificate onto back to Non-Windows Device

1. With **privileged account**, log into the (web) management interface of target device
2. Navigate to **Security** or **Certificate** section of interface
3. Select option to **Import Signed Certificate / Upload Certificate / Install Certificate**
   1. These options may also be found in the **Configure Certificate** section of interface
4. Upload the appropriate signed **.cer/.crt** file for the target device
   1. If available, uncheck **Mark private key as exportable** or something along those lines
5. If prompted, click **Apply / Save / Restart Network Services**
   1. **Device's web server may restart to apply the new certificate**

## Verification

1. Reopen browser to clear any old cached certificate errors
2. Navigate to target device's (web) management interface
3. Click the lock icon in the address bar
4. Ensure certificate details match our CA environment as well as the hash algorithm and key length for the chosen template

</div>