---
title: 'Azure AD Connect: Update the SSL certificate for an Active Directory Federation Services (AD FS) farm | Microsoft Docs'
description: This document details the steps to update the SSL certificate of an AD FS farm by using Azure AD Connect.
services: active-directory
keywords: azure ad connect, adfs ssl update, adfs certificate update, change adfs certificate, new adfs certificate, adfs certificate, update adfs ssl certificate, update ssl certificate adfs, configure adfs ssl certificate, adfs, ssl, certificate, adfs service communication certificate, update federation, configure federation, aad connect
authors: anandyadavmsft
manager: femila
editor: billmath

ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory  
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: anandy

---    

# Update the SSL certificate for an Active Directory Federation Services (AD FS) farm

## Overview
This article describes how you can use Azure AD Connect to update the SSL certificate for an Active Directory Federation Services (AD FS) farm. You can use the Azure AD Connect tool to easily update the SSL certificate for the AD FS farm even if the user sign-in method selected is not AD FS.

You can perform the whole operation of updating SSL certificate for the AD FS farm across all federation and Web Application Proxy (WAP) servers in three simple steps:

![Three steps](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>To learn more about certificates that are used by AD FS, see [Understanding certificates used by AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## Prerequisites

* **AD FS Farm**: Make sure that your AD FS farm is Windows Server 2012 R2-based or later.
* **Azure AD Connect**: Ensure that the version of Azure AD Connect is 1.1.443.0 or later. You'll use the task **Update AD FS SSL certificate**.

![Update SSL task](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

## Step 1: Provide AD FS farm information

Azure AD Connect attempts to obtain information about the AD FS farm automatically by:
1. Querying the farm information from AD FS (Windows Server 2016 or later).
2. Referencing the information from previous runs, which are stored locally with Azure AD Connect.

You can modify the list of servers that are displayed by adding or removing the servers to reflect the current configuration of the AD FS farm. As soon as the server information is provided, Azure AD Connect displays the connectivity and current SSL certificate status.

![AD FS server info](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

If the list contains a server that's no longer part of the AD FS farm, click **Remove** to delete the server from the list of servers in your AD FS farm.

![Offline server in list](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Removing a server from the list of servers for an AD FS farm in Azure AD Connect is a local operation and updates the information for the AD FS farm that Azure AD Connect maintains locally. Azure AD Connect doesn't modify the configuration on AD FS to reflect the change.    

## Step 2: Provide a new SSL certificate

After you've confirmed the information about AD FS farm servers, Azure AD Connect asks for the new SSL certificate. Provide a password-protected PFX certificate to continue the installation.

![SSL certificate](./media/active-directory-aadconnectfed-ssl-update/certificate.png)

After you provide the certificate, Azure AD Connect goes through a series of prerequisites. Verify the certificate to ensure that the certificate is correct for the AD FS farm:

-	The subject name/alternate subject name for the certificate is either the same as the federation service name, or it's a wildcard certificate.
-	The certificate is valid for more than 30 days.
-	The certificate trust chain is valid.
-	The certificate is password protected.

## Step 3: Select servers for the update

In the next step, select the servers that need to have the SSL certificate updated. Servers that are offline can't be selected for the update.

![Select servers to update](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

After you complete the configuration, Azure AD Connect displays the message that indicates the status of the update and provides an option to verify the AD FS sign-in.

![Configuration complete](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

## FAQs

* **What should be the subject name of the certificate for the new AD FS SSL certificate?**

    Azure AD Connect checks if the subject name/alternate subject name of the certificate contains the federation service name. For example, if your federation service name is fs.contoso.com, the subject name/alternate subject name must be fs.contoso.com.  Wildcard certificates are also accepted.

* **Why am I asked for credentials again on the WAP server page?**

    If the credentials you provide for connecting to AD FS servers don't also have the privilege to manage the WAP servers, then Azure AD Connect asks for credentials that have administrative privilege on the WAP servers.

* **The server is shown as offline. What should I do?**

    Azure AD Connect can't perform any operation if the server is offline. If the server is part of the AD FS farm, then check the connectivity to the server. After you've resolved the issue, press the refresh icon to update the status in the wizard. If the server was part of the farm earlier but now no longer exists, click **Remove** to delete it from the list of servers that Azure AD Connect maintains. Removing the server from the list in Azure AD Connect doesn't alter the AD FS configuration itself. If you're using AD FS in Windows Server 2016 or later, the server remains in the configuration settings and will be shown again the next time the task is run.

* **Can I update a subset of my farm servers with the new SSL certificate?**

    Yes. You can always run the task **Update SSL Certificate** again to update the remaining servers. On the **Select servers for SSL certificate update** page, you can sort the list of servers on **SSL Expiry date** to easily access the servers that aren't updated yet.

* **I removed the server in the previous run, but it's still being shown as offline and listed on the AD FS Servers page. Why is the offline server still there even after I removed it?**

    Removing the server from the list in Azure AD Connect doesn't remove it in the AD FS configuration. Azure AD Connect references AD FS (Windows Server 2016 or higher) for any information about the farm. If the server is still present in the AD FS configuration, it will be listed back in the list.  

## Next steps

- [Azure AD Connect and federation](active-directory-aadconnectfed-whatis.md)
- [Active Directory Federation Services management and customization with Azure AD Connect](active-directory-aadconnect-federation-management.md)
