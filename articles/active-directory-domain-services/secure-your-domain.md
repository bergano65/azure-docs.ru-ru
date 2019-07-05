---
title: 'Защита доменных служб Azure Active Directory: администрирование управляемого домена | Документация Майкрософт'
description: Защита управляемого домена
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: e94cd9ca049cfdfd2321ce046714506ed1f23390
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483280"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Защита управляемого домена доменных служб Azure AD
Эта статья поможет вам применить защиту управляемого домена. Можно отключить использование легко раскрываемых шифров наборов текстов и отключить хэш-синхронизацию учетных данных NTLM.

## <a name="install-the-required-powershell-modules"></a>Установка необходимых модулей PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Установка и настройка Azure PowerShell
Выполните инструкции из статьи [об установке модуля Azure AD PowerShell и подключении к Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Установка и настройка Azure PowerShell
Выполните инструкции из статьи [Установка и настройка Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Отключите легко раскрываемые шифры наборов текстов и синхронизацию хэшей учетных данных NTLM
С помощью приведенного ниже сценария PowerShell можно сделать следующее:
1. отключить поддержку NTLM v1 в управляемом домене;
2. отключить синхронизацию хэшей паролей NTLM в локальном экземпляре Active Directory;
3. отключить TLS v1 в управляемом домене.

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

> [!IMPORTANT]
> Пользователи (и учетные записи служб) нельзя выполнить простой привязки LDAP, если вы отключили синхронизацию хэшей паролей NTLM экземпляр доменных служб Azure AD.  Дополнительные сведения об отключении синхронизации хэшей паролей NTLM [защиты управляемого домена доменных служб Azure AD](secure-your-domain.md).
>
>

## <a name="next-steps"></a>Дальнейшие действия
* [Синхронизация в управляемом домене доменных служб Azure AD](synchronization.md)
