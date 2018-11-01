---
title: 'Защита доменных служб Azure Active Directory: администрирование управляемого домена | Документация Майкрософт'
description: Защита управляемого домена
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: 1dea3def00423ecf092562aa47c76258316194a9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50159066"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Защита управляемого домена доменных служб Azure AD
Эта статья поможет вам применить защиту управляемого домена. Можно отключить использование легко раскрываемых шифров наборов текстов и отключить хэш-синхронизацию учетных данных NTLM.

## <a name="install-the-required-powershell-modules"></a>Установка необходимых модулей PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Установка и настройка Azure PowerShell
Выполните инструкции из статьи [об установке модуля Azure AD PowerShell и подключении к Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Установка и настройка Azure PowerShell
Выполните инструкции из статьи [Установка и настройка Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Отключите легко раскрываемые шифры наборов текстов и синхронизацию хэшей учетных данных NTLM
С помощью приведенного ниже сценария PowerShell можно сделать следующее:
1. отключить поддержку NTLM v1 в управляемом домене;
2. отключить синхронизацию хэшей паролей NTLM в локальном экземпляре Active Directory;
3. отключить TLS v1 в управляемом домене.

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>Дополнительная информация
* [Синхронизация в управляемом домене доменных служб Azure AD](active-directory-ds-synchronization.md)
