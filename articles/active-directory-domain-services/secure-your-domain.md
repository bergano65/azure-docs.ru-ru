---
title: Безопасные службы домена Azure AD (ru) Документы Майкрософт
description: Узнайте, как отключить слабые шифры, старые протоколы и синхронизацию хэш-хэша паролем NTLM для домена Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 8eee516beaaf26ed25bd20f9689d26fdb1eb9b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74546221"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Отключить слабые шифры и синхронизацию паролей для обеспечения безопасности домена Azure AD Domain Services, управляемого домена

По умолчанию службы домена Active Directory Domain Services Azure (Azure AD DS) позволяют использовать шифры, такие как NTLM v1 и TLS v1. Эти шифры могут потребоваться для некоторых устаревших приложений, но считаются слабыми и могут быть отключены, если они вам не нужны. Если у вас есть предварительное гибридное подключение с помощью Azure AD Connect, вы также можете отключить синхронизацию хэшей паролем NTLM.

В этой статье показано, как отключить шифры NTLM v1 и TLS v1 и отключить синхронизацию хэша паролем NTLM.

## <a name="prerequisites"></a>Предварительные требования

Для завершения этой статьи необходимы следующие ресурсы:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, создайте [учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * Если потребуется, [создайте и настройте экземпляр доменных служб Azure Active Directory][create-azure-ad-ds-instance].
* Установите и настройте Azure PowerShell.
    * При необходимости следуйте [инструкциям по установке модуля Azure PowerShell и подключитесь к подписке Azure.](/powershell/azure/install-az-ps)
    * Убедитесь, что вы впишетесь в подписку Azure с помощью cmdlet [Connect-AzAccount.][Connect-AzAccount]
* Установка и настройка Azure AD PowerShell.
    * При необходимости следуйте инструкциям [по установке модуля Azure AD PowerShell и подключитесь к Azure AD.](/powershell/azure/active-directory/install-adv2)
    * Убедитесь, что вы впишетесь в ваш aure AD-арендатора с помощью [cmdlet Connect-AzureAD.][Connect-AzureAD]

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Отключить слабые шифры и синхронизацию хэша паролем NTLM

Чтобы отключить слабые наборы шифров и синхронизацию хэш-хэш-хэша NTLM, вопийте на учетную запись Azure, а затем получите ресурс Azure AD DS с помощью cmdlet [Get-AzResource:][Get-AzResource]

> [!TIP]
> Если вы получили ошибку с помощью команды [Get-AzResource,][Get-AzResource] что ресурс *Microsoft.AAD/DomainServices* не существует, [повысить доступ к управлению всеми подписями Azure и группами управления.][global-admin]

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Далее определите *DomainSecuritySettings* для настройки следующих параметров безопасности:

1. Отключить поддержку NTLM v1.
2. отключить синхронизацию хэшей паролей NTLM в локальном экземпляре Active Directory;
3. Отключить TLS v1.

> [!IMPORTANT]
> Пользователи и учетные записи служб не могут выполнять простые связывания LDAP, если вы отстраняете синхронизацию хэш-хэша NTLM в домене Azure AD DS. Если вам нужно выполнить простые привязки LDAP, не устанавливайте *"SyncNtlmPasswords" -"Инвалид";* параметр конфигурации безопасности в следующей команде.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Наконец, примените определенные параметры безопасности к управляемому домемену Azure AD DS с помощью cmdlet [Set-AzResource.][Set-AzResource] Укажите ресурс Azure AD DS с первого шага и параметры безопасности предыдущего шага.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Требуется несколько минут, чтобы настройки безопасности были применены к домену Azure AD DS.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о процессе синхронизации, см., [как объекты и учетные данные синхронизируются в домене Azure AD DS.][synchronization]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD