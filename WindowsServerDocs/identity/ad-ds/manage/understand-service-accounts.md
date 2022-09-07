---
title: Service Accounts
description: Windows Server standalone and group-managed service accounts in Active Directory
author: dansimp
ms.author: dansimp
ms.topic: article
ms.date: 07/21/2022
---

# Service accounts

>Applies to: Windows Server 2022, Windows Server 2019, Windows Server 2016

This article explains group and standalone managed service accounts, computer-specific virtual computer account, and it points to resources about these service accounts.

## Overview

A service account is a user account that is created explicitly to provide a security context for services running on Windows Server operating systems. The security context determines the service's ability to access local and network resources. The Windows operating systems rely on services to run various features. These services can be configured through the applications, the Services snap-in, or Task Manager, or by using Windows PowerShell.

This article contains information about the following types of service accounts:

- [Standalone managed service accounts](#standalone-managed-service-accounts)

- [Group-managed service accounts](#group-managed-service-accounts)

- [Virtual accounts](#virtual-accounts)

### Standalone managed service accounts

A managed service account is designed to isolate domain accounts in crucial applications, such as Internet Information Services (IIS), and eliminate the need for an administrator to manually administer the service principal name (SPN) and credentials for the accounts.

To use managed service accounts, the server on which the application or service is installed must be running at least Windows Server 2008 R2. One managed service account can be used for services on a single computer. Managed service accounts cannot be shared between multiple computers, and they cannot be used in server clusters where a service is replicated on multiple cluster nodes. For this scenario, you must use a group-managed service account. For more information, see [Group-Managed Service Accounts Overview](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)).

In addition to the enhanced security that is provided by having individual accounts for critical services, there are four important administrative benefits associated with managed service accounts:

- You can create a class of domain accounts that can be used to manage and maintain services on local computers.

- Unlike domain accounts in which administrators must manually reset passwords, the network passwords for these accounts are automatically reset.

- You do not have to complete complex SPN management tasks to use managed service accounts.

- You don't have to complete complex SPN management tasks to use managed service accounts.

- Administrative tasks for managed service accounts can be delegated to non-administrators.

### Software requirements

Managed service accounts apply to the Windows operating systems that are designated in the **Applies To** list at the beginning of this article.

### Group-managed service accounts

Group-managed service accounts are an extension of the standalone-managed service accounts, which were introduced in Windows Server 2008 R2. These accounts are managed domain accounts that provide automatic password management and simplified service principal name (SPN) management, including delegation of management to other administrators.

The group-managed service account provides the same functionality as a standalone managed service account within the domain, but it extends that functionality over multiple servers. When connecting to a service that is hosted on a server farm, such as Network Load Balancing, the authentication protocols that support mutual authentication require all instances of the services to use the same principal. When group-managed service accounts are used as service principals, the Windows Server operating system manages the password for the account instead of relying on the administrator to manage the password.

The Microsoft Key Distribution Service (kdssvc.dll) provides the mechanism to securely obtain the latest key or a specific key with a key identifier for an Active Directory account. This service was introduced in Windows Server 2012, and it does not run on previous versions of the Windows Server operating system. The Key Distribution Service shares a secret, which is used to create keys for the account. These keys are periodically changed. For a group-managed service account, the domain controller computes the password on the key that is provided by the Key Distribution Services, in addition to other attributes of the group-managed service account.

### Group-managed practical applications

Group-managed service accounts provide a single identity solution for services running on a server farm, or on systems that use Network Load Balancing. By providing a group-managed service account solution, services can be configured for the group-managed service account principal, and the password management is handled by the operating system.

Using a group-managed service account, service administrators do not need to manage password synchronization between service instances. The group-managed service account supports hosts that are kept offline for an extended time period and the management of member hosts for all instances of a service. This provision means that you can deploy a server farm that supports a single identity to which existing client computers can authenticate without knowing the instance of the service to which they are connecting.

Failover clusters do not support group-managed service accounts. However, services that run on top of the Cluster service can use a group-managed service account or a standalone managed service account if they are a Windows service, an App pool, a scheduled task, or if they natively support group-managed service account or standalone managed service accounts.

### Group-managed software requirements

Group-managed service accounts can only be configured and administered on computers running at least Windows Server 2012, but they can be deployed as a single service identity solution in domains that still have domain controllers running operating systems earlier than Windows Server 2012. There are no domain or forest functional level requirements.

A 64-bit architecture is required to run the Windows PowerShell commands that are used to administer group-managed service accounts.

A managed service account is dependent on encryption types supported by Kerberos. When a client computer authenticates to a server by using Kerberos protocol, the domain controller creates a Kerberos service ticket that is protected with encryption that the domain controller and the server support. The domain controller uses the account’s **msDS-SupportedEncryptionTypes** attribute to determine what encryption the server supports, and if there is no attribute, it assumes that the client computer does not support stronger encryption types. The Advanced Encryption Standard (AES) must always be configured for managed service accounts. If computers that host the managed service account are configured to not support RC4, authentication will always fail.

> [!NOTE]
> Introduced in Windows Server 2008 R2, the Data Encryption Standard (DES) is disabled by default. Group-managed service accounts are not applicable in Windows operating systems prior to Windows Server 2012.

For more information about supported encryption types, see [Changes in Kerberos Authentication](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560670(v=ws.10)).

### Virtual accounts

Virtual accounts were introduced in Windows Server 2008 R2 and Windows 7, and are managed local accounts that provide the following features to simplify service administration:

- The virtual account is automatically managed.

- The virtual account can access the network in a domain environment.

- No password management is required. For example, if the default value is used for the service accounts during SQL Server setup on Windows Server 2008 R2, a virtual account that uses the instance name as the service name is established in the format NT SERVICE\\&lt;SERVICENAME&gt;.

Services that run as virtual accounts access network resources by using the credentials of the computer account in the format &lt;domain\_name&gt;\\&lt;computer\_name&gt;$.

For information about how to configure and use virtual service accounts, see [Service Accounts Step-by-Step Guide](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10)).

### Virtual accounts software requirements

Virtual accounts apply to the Windows operating systems that are designated in the **Applies To** list at the beginning of this article.

### See also

The following table provides links to other resources that are related to standalone managed service accounts, group-managed service accounts, and virtual accounts:

| Content type  | References  |
|---------------|-------------|
| **Product evaluation** | [What's New for Managed Service Accounts](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831451(v=ws.11))<p>[Getting Started with Group Managed Service Accounts](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)) |
| **Deployment** | [Windows Server 2012: Group Managed Service Accounts - Ask Premier Field Engineering (PFE) Platforms - Site Home - TechNet Blogs](https://blogs.technet.com/b/askpfeplat/archive/2012/12/17/windows-server-2012-group-managed-service-accounts.aspx) |
| **Related technologies** | [Security Principals](understand-security-principals.md)<p>[What's new in Active Directory Domain Services](/windows-server/identity/whats-new-active-directory-domain-services) |