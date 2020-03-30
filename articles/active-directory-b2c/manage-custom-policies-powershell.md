---
title: Управление пользовательскими политиками с помощью PowerShell
titleSuffix: Azure AD B2C
description: Используйте azure Active Directory (Azure AD) PowerShell cmdlet для программного управления пользовательскими политиками Azure AD B2C. Создавайте, читайте, обновляйте и удаляйте пользовательские политики с помощью PowerShell.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf0cfffa410d8dfe2f0e0b42a0fee0c16106fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187412"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Управление пользовательскими политиками Azure AD B2C с помощью Azure PowerShell

Azure PowerShell предоставляет несколько cmdlets для управления пользовательскими политиками на основе командной строки и скрипта в вашем арендаторе Azure AD B2C. Узнайте, как использовать модуль Azure AD PowerShell для:

* Перечислите пользовательские политики в наемнии Azure AD B2C
* Скачать политику у арендатора
* Обновление существующей политики путем перезаписи ее содержимого
* Загрузите новую политику для арендатора Azure AD B2C
* Удаление пользовательской политики от клиента

## <a name="prerequisites"></a>Предварительные требования

* [Арендатор Azure AD B2C](tutorial-create-tenant.md)и учетные данные пользователя в каталоге с ролью [администратора политики B2C IEF](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Пользовательские политики,](custom-policy-get-started.md) загруженные арендатору
* [Azure AD PowerShell для **модуля предварительного просмотра** графика](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Подключение сеанса PowerShell к арендатору B2C

Чтобы работать с пользовательскими политиками в вашем арендаторе Azure AD B2C, сначала необходимо подключить сеанс PowerShell к арендатору с помощью команды [Connect-AzureAD.][Connect-AzureAD]

Выполните следующую команду, `{b2c-tenant-name}` заменив имя вашего арендатора Azure AD B2C. Восватрите с учетной записью, которая присваивает роль [администратора политики B2C IEF](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) в каталоге.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Пример вывода команд, показывающий успешный входин:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Перечислите все пользовательские политики в арендаторе

Обнаружение пользовательских политик позволяет администратору Azure AD B2C просматривать, управлять и добавлять бизнес-логику в свои операции. Используйте команду [Get-AzureADMSTrustFrameworkPolicy,][Get-AzureADMSTrustFrameworkPolicy] чтобы вернуть список ивентий пользовательских политик в наемнии Azure AD B2C.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Вывод команды примера:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Скачать политику

После просмотра списка интидозаписей политики можно настроить таргетинг на конкретную политику с [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] для загрузки ее содержимого.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

В этом примере загружается политика с id *B2C_1A_signup_signin:*

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Чтобы отсеять содержимое политики локально, прокладка вывода команды в файл с `-OutputFilePath` аргументом, а затем открыть файл в вашем любимом редакторе.

Пример команды отправки вывода в файл:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Обновление существующей политики

После редактирования созданного или загруженного файла политики можно опубликовать обновленную политику в Azure AD B2C с помощью команды [Set-AzureADMSTrustFrameworkPolicy.][Set-AzureADMSTrustFrameworkPolicy]

Если вы `Set-AzureADMSTrustFrameworkPolicy` оформляете команду с идентификатором политики, который уже существует в вашем наемщике Azure AD B2C, содержание этой политики перезаписано.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Пример команды:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Дополнительные примеры можно найти в справочнике команды [Set-AzureADMSTrustFrameworkPolicy.][Set-AzureADMSTrustFrameworkPolicy]

## <a name="upload-a-new-policy"></a>Загрузить новую политику

При изменении пользовательской политики, которая работает в производственной, может потребоваться опубликовать несколько версий политики для сценариев резервного копирования или A/B-тестирования. Или вы можете сделать копию существующей политики, изменить ее с помощью нескольких небольших изменений, а затем загрузить ее в качестве новой политики для использования другим приложением.

Используйте команду [New-AzureADMSTrustFrameworkPolicy,][New-AzureADMSTrustFrameworkPolicy] чтобы загрузить новую политику:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Пример команды:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Удаление пользовательской политики

Для поддержания жизненного цикла операций мы рекомендуем периодически удалять неиспользованные пользовательские политики. Например, можно удалить старые версии политики после выполнения миграции в новый набор политик и проверки функциональности новых политик. Кроме того, если вы попытаетесь опубликовать набор пользовательских политик и получить ошибку, возможно, имеет смысл удалить политики, которые были созданы в рамках неудавшегося выпуска.

Используйте команду [Remove-AzureADMSTrustFrameworkPolicy,][Remove-AzureADMSTrustFrameworkPolicy] чтобы удалить политику из клиента.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Пример команды:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Загрузка политики устранения неполадок

При попытке опубликовать новую пользовательскую политику или обновить существующую политику неправильное форматирование XML и ошибки в цепочке наследования файла политики могут привести к сбоям проверки.

Например, вот попытка обновить политику с содержанием, содержащим пороки xML (выход усечен для краткости):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Для получения информации о пользовательских политиках устранения неполадок [см.](active-directory-b2c-guide-troubleshooting-custom.md)

## <a name="next-steps"></a>Дальнейшие действия

Для получения информации об использовании PowerShell для развертывания пользовательских политик в рамках непрерывной интеграции/непрерывной доставки (CI/CD) — [см. Пользовательские политики Deploy из конвейера Azure DevOps.](deploy-custom-policies-devops.md)

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy
