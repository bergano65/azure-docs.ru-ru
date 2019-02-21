---
title: Создание удостоверения для приложения Azure с помощью PowerShell | Документация Майкрософт
description: Использование Azure PowerShell для создания приложения Azure Active Directory и субъекта-службы с последующим предоставлением доступа к ресурсам посредством управления доступом на основе ролей. В статье показано, как выполнять аутентификацию приложения с помощью сертификата.
services: active-directory
documentationcenter: na
author: CelesteDG
manager: mtillman
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/24/2018
ms.author: celested
ms.reviewer: tomfitz
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12bfcea70c80929ade656bc5e23f8b95fce44a54
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312163"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Практическое руководство: Использование Azure PowerShell для создания субъекта-службы с сертификатом

При наличии приложения или сценария, которому требуется доступ к ресурсам, можно настроить удостоверение для приложения и аутентифицировать его с помощью собственных учетных данных. Этот идентификатор известен как субъект-служба. Такой подход позволяет выполнить следующие действия:

* Назначить удостоверению приложения разрешения, которые отличаются от ваших разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы.
* Использовать сертификат для аутентификации при выполнении автоматического сценария.

> [!IMPORTANT]
> Вместо создания субъекта-службы вы можете применить управляемые удостоверения ресурсов Azure в качестве удостоверения приложения. Если код выполняется в службе, которая поддерживает управляемые удостоверения и обращается к ресурсам, которые поддерживают проверку подлинности Azure Active Directory (Azure AD), то управляемые удостоверения будут оптимальным выбором. Дополнительные сведения об управляемых удостоверениях для ресурсов Azure, включая службы, которые в настоящее время поддерживают их, см. в разделе [Что такое управляемые удостоверения для ресурсов Azure?](../managed-identities-azure-resources/overview.md)

В этой статье показано, как создать субъект-службу, который выполняет аутентификацию с помощью сертификата. Настройка субъекта-службы с паролем описана в статье [Создание субъекта-службы Azure с помощью Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Для выполнения задач из этой статься требуется Azure PowerShell [последней версии](/powershell/azure/install-az-ps).

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Необходимые разрешения

Для работы с этой статьей у вас должен быть достаточный уровень разрешений в подписках Azure AD и Azure. В частности, вы должны иметь право на создание приложения в Azure AD и назначение роли субъекту-службе.

Проверить, есть ли у вас соответствующие разрешения, проще всего на портале. Ознакомьтесь с [проверкой наличия необходимых разрешений](howto-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Создание субъекта-службы с самозаверяющим сертификатом

Ниже приводится простой пример сценария. В нем используется команда [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal), чтобы создать субъект-службу с самозаверяющим сертификатом, а затем выполняется командлет [New-AzureRmRoleAssignment](/powershell/module/az.resources/new-azroleassignment) для присвоения этому субъекту-службе роли [участника](../../role-based-access-control/built-in-roles.md#contributor). Назначение ролей ограничивается текущей выбранной подпиской Azure. Чтобы выбрать другую подписку, выполните командлет [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Пример бездействует 20 секунд, чтобы данные нового субъекта-службы распространились в Azure AD. Если сценарий не подождет достаточно долго, появится сообщение об ошибке. "Субъект {ИД} не существует в каталоге {ИД каталога}". Чтобы устранить эту ошибку, подождите немного и выполните команду **New-AzRoleAssignment** еще раз.

Вы можете задать область назначения ролей для определенной группы ресурсов с помощью параметра **ResourceGroupName**. Кроме того, вы можете задать область для определенного ресурса с помощью параметров **ResourceType** и **ResourceName**. 

Если вы **не используете Windows 10 или Windows Server 2016**, скачайте сценарий [генератора самозаверяющих сертификатов](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) из центра скриптов Microsoft. Извлеките содержимое сценария и импортируйте требуемый командлет.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Замените в сценарии следующие две строки, чтобы создать сертификат.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Предоставление сертификата с помощью автоматизированного сценария PowerShell

При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Azure AD.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Создание субъекта-службы с помощью сертификата из центра сертификации

В приведенном ниже примере создается субъект-служба с сертификатом, выданным центром сертификации. Назначение ограничено указанной подпиской Azure. Также в примере этот субъект-служба добавляется в роль [участника](../../role-based-access-control/built-in-roles.md#contributor). Если при назначении ролей возникнет ошибка, назначение выполняется повторно.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Предоставление сертификата с помощью автоматизированного сценария PowerShell
При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Azure AD.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Идентификаторы приложения и клиента не являются конфиденциальными, поэтому их можно внедрить непосредственно в скрипт. Чтобы получить идентификатор клиента, используйте следующий командлет:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Чтобы получить идентификатор приложения, используйте следующий командлет:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Изменение учетных данных

Чтобы изменить учетные данные для приложения AD из-за нарушения безопасности или истечения их срока действия, используйте командлеты [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) и [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential).

Чтобы удалить все учетные данные для приложения, используйте следующую команду.

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Чтобы добавить значение сертификата, создайте самозаверяющий сертификат, как показано в этой статье. Затем используйте следующую команду.

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Отладка

При создании субъекта-службы могут возникнуть следующие ошибки.

* **"Authentication_Unauthorized"** (Аутентификация не выполнена) или **"No subscription found in the context"** (В контексте не удалось найти подписку). Эта ошибка может отобразиться, если учетная запись не имеет [необходимых разрешений](#required-permissions) в Azure AD для регистрации приложения. Как правило, она возникает в тех случаях, когда регистрировать приложения в Azure Active Directory могут только администраторы, а ваша учетная запись не является административной. Попросите администратора назначить вам роль администратора или разрешить пользователям регистрировать приложения.

* Ваша учетная запись **"не авторизована для выполнения действия 'Microsoft.Authorization/roleAssignments/write' с областью '/subscriptions/{guid}'."** Эта ошибка возникает, когда учетная запись не имеет достаточно разрешений для назначения роли удостоверению. Попросите администратора подписки назначить вам роль администратора доступа пользователей.

## <a name="next-steps"></a>Дополнительная информация

* Настройка субъекта-службы с паролем описана в статье [Создание субъекта-службы Azure с помощью Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Подробные инструкции по интеграции приложения в Azure для управления ресурсами см. в [Управление ресурсами клиента с помощью Azure Active Directory и Resource Manager](../../azure-resource-manager/resource-manager-api-authentication.md).
* Более подробное описание приложений и субъектов-служб см. в статье [Объекты приложений и объекты участников-служб](app-objects-and-service-principals.md).
* Дополнительные сведения о проверке подлинности Azure AD см. [здесь](authentication-scenarios.md).
