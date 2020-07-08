---
title: Настройка согласия конечных пользователей для приложений с помощью Azure Active Directory
description: Узнайте, как управлять согласием пользователей на приложения, которые будут иметь доступ к данным вашей организации.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0c9844d5e3f65dba5e51170367cfd16715a08883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763471"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Настройка согласия конечных пользователей для приложений

Вы можете интегрировать приложения с платформой удостоверений Microsoft, чтобы пользователи могли выполнять вход с рабочей или учебной учетной записью и получать доступ к данным вашей организации для использования расширенных возможностей, предоставляемых данными.

Прежде чем приложение сможет получить доступ к данным организации, пользователь должен предоставить ему разрешения. Различные разрешения позволяют использовать разные уровни доступа. По умолчанию всем пользователям разрешено предоставлять согласие на разрешения приложений, не требующие согласия администратора. Например, по умолчанию пользователь может предоставить приложению разрешение на доступ к своему почтовому ящику, но не может дать приложению неограниченный доступ на чтение и запись для всех файлов в организации.

Позволяя пользователям предоставлять приложениям доступ к данным, вы даете им возможность легко получать полезные приложения и работать продуктивно. Однако в некоторых ситуациях такая конфигурация может представлять риск, поэтому нужно внимательно отслеживать и контролировать такие разрешения.

## <a name="user-consent-settings"></a>Параметры согласия пользователя

Чтобы управлять тем, в каких случаях пользователи могут давать согласие приложениям, выберите политику согласия, которая будет применяться ко всем пользователям. Ниже приведены три варианта политики разрешения.

* **Отключить согласие пользователя** — пользователи не могут предоставлять приложениям разрешения. Пользователи могут входить в приложения, на которые они согласились ранее или на которые дал согласие администратор от их имени, но они не смогут согласиться на новые разрешения или приложения самостоятельно. Только пользователи, которым предоставлена роль каталога, включающая разрешение на предоставление согласия, смогут дать согласие на новые разрешения или новые приложения.

* **Пользователи могут предоставлять согласие только приложениям от проверенных издателей, но давать только выбранные вами разрешения (предварительная версия)**  — все пользователи могут предоставлять согласие только приложениям, которые были опубликованы [проверенным издателем](../develop/publisher-verification-overview.md), и приложениям, зарегистрированным в вашем клиенте. Пользователи могут согласиться только на разрешения из категории "Низкое влияние".

  Обязательно [разделите разрешения на категории](#configure-permission-classifications-preview), чтобы выбрать, какие разрешения могут предоставлять пользователи.

* **Пользователи могут предоставлять согласие всем приложениям**. Этот параметр позволяет всем пользователям предоставлять любому приложению согласие на любое разрешение, которое не требует согласия администратора. 

   Чтобы снизить риск того, что вредоносные приложения попытаются обманным путем заставить пользователей предоставить им доступ к данным организации, рекомендуется разрешить согласие пользователя только для приложений, опубликованных [проверенным издателем](../develop/publisher-verification-overview.md).

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Настройка параметров согласия пользователя на портале Azure

Настройка параметров согласия пользователя на портале Azure:

1. Войдите на [портал Azure](https://portal.azure.com) как [глобальный администратор](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Выберите **Azure Active Directory** > **Корпоративные приложения** > **Согласия и разрешения** > **Параметры согласия пользователя**.
1. В разделе **Согласие пользователя для приложений** выберите параметр согласия, который вы хотите настроить для всех пользователей.
1. Нажмите кнопку **Save** (Сохранить), чтобы сохранить настройки.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Параметры согласия пользователя":::

> [!TIP]
> Рассмотрите возможность [включения рабочего процесса согласия администратора](configure-admin-consent-workflow.md), чтобы разрешить пользователям запрашивать у администратора проверку и утверждение приложения, которому пользователю не разрешено предоставлять согласие, например, когда согласие пользователя было отключено или когда приложение запрашивает разрешения, которые пользователю запрещено предоставлять.

### <a name="configure-user-consent-settings-using-powershell"></a>Настройка параметров согласия пользователя с помощью PowerShell

Вы можете использовать последний модуль предварительной версии Azure AD PowerShell, [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), чтобы выбрать политику согласия, определяющую согласие пользователей для приложений.

* **Отключить согласие пользователя**. Чтобы отключить согласие пользователя, установите пустые политики согласия, которые регулируют согласие пользователя:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **Разрешить согласие пользователей для приложений от проверенных издателей для выбранных разрешений (предварительная версия)** . Чтобы разрешить ограниченное согласие пользователей только для приложений от проверенных издателей и приложений, зарегистрированных в клиенте, и только для разрешений в категории "Низкое влияние", настройте встроенную политику согласия с именем `microsoft-user-default-low`.

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   Обязательно [разделите разрешения на категории](#configure-permission-classifications-preview), чтобы выбрать, какие разрешения могут предоставлять пользователи.

* **Разрешить согласие пользователей для всех приложений** — разрешите согласие пользователей для всех приложений.

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   Этот параметр позволяет всем пользователям предоставлять любому приложению согласие на любое разрешение, которое не требует согласия администратора. Рекомендуется разрешить согласие пользователя только для приложений от проверенных издателей.

## <a name="configure-permission-classifications-preview"></a>Настройка классификации разрешений (предварительная версия)

Классификация разрешений позволяет определить влияние различных разрешений в соответствии с политиками и оценками рисков вашей организации. Например, можно использовать классификацию разрешений в политиках согласия, чтобы определить набор разрешений, на которые пользователи могут предоставлять согласие.

> [!NOTE]
> В настоящее время поддерживается только категория разрешений "Низкое влияние". Только делегированные разрешения, не требующие согласия администратора, можно отнести к категории "Низкое влияние".

### <a name="classify-permissions-using-the-azure-portal"></a>Классификация разрешений на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com) как [глобальный администратор](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Выберите **Azure Active Directory** > **Корпоративные приложения** > **Согласия и разрешения** > **Классификация разрешений**.
1. Выберите **Добавить разрешения**, чтобы отнести разрешение в категорию "Низкое влияние". 
1. Выберите API и делегированные разрешения.

В этом примере мы классифицировали минимальный набор разрешений, необходимых для единого входа:

:::image type="content" source="media/configure-user-consent/permission-classifications.png" alt-text="Классификации разрешений":::

> [!TIP]
> Для Microsoft Graph API минимальными разрешениями, необходимыми для простого единого входа, являются `openid`, `profile`, `User.Read` и `offline_access`. С этими разрешениями приложение может считывать сведения о профиле пользователя, выполнившего вход, и поддерживать этот доступ, даже если пользователь больше не использует приложение.

### <a name="classify-permissions-using-powershell"></a>Классификация разрешений с помощью PowerShell

Вы можете использовать последний модуль предварительной версии Azure AD PowerShell, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview), чтобы классифицировать разрешения. Классификации разрешений настраиваются на объекте **ServicePrincipal** интерфейса API, который публикует разрешения.

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>Чтение текущей классификации разрешений для API:

1. Извлеките объект **ServicePrincipal** для API. Здесь мы извлекаем объект ServicePrincipal для API Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Чтение классификации делегированных разрешений для API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>Чтобы отнести разрешение в категорию "Низкое влияние":

1. Извлеките объект **ServicePrincipal** для API. Здесь мы извлекаем объект ServicePrincipal для API Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Найдите делегированное разрешение, которому вы хотите присвоить категорию:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Задайте категорию разрешения с помощью его имени и идентификатора:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>Удаление категории делегированных разрешений:

1. Извлеките объект **ServicePrincipal** для API. Здесь мы извлекаем объект ServicePrincipal для API Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Найдите категорию делегированных разрешений, которую вы хотите удалить:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Удаление категории разрешений:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Настройка согласия владельца группы на приложения, обращающиеся к данным группы

Владельцы групп могут авторизовать приложения, например приложения, опубликованные сторонними поставщиками, для доступа к данным организации, связанным с группой. Например, владелец группы в Microsoft Teams может разрешить приложению читать все сообщения групп в Teams или выводить базовые профили участников группы.

Можно настроить, каким пользователям разрешено предоставлять согласие приложениям, обращающимся к данным группы, или отключить эту функцию.

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Настройка согласия владельца группы на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com) как [глобальный администратор](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Выберите **Azure Active Directory** > **Корпоративные приложения** > **Согласия и разрешения** > **Параметры согласия пользователя**.
3. В разделе **Согласие владельца группы для приложений, обращающихся к данным** выберите параметр, который вы хотите включить.
4. Нажмите кнопку **Save** (Сохранить), чтобы сохранить настройки.

В этом примере всем владельцам групп разрешено давать согласие на доступ приложений к данным группы:

:::image type="content" source="media/configure-user-consent/group-owner-consent.png" alt-text="Параметры согласия владельца группы":::

### <a name="configure-group-owner-consent-using-powershell"></a>Настройка согласия владельца группы с помощью PowerShell

Вы можете использовать модуль предварительной версии Azure AD PowerShell, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview), чтобы разрешить или запретить владельцам групп предоставлять согласие приложениям, обращающимся к данным организации, для групп, которыми они владеют.

1. Убедитесь, что используете модуль [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview). Этот важно, если вы установили модуль [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) и модуль [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Подключитесь к Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Получите текущее значение параметров каталога **Параметры политики согласия** в клиенте. Для этого необходимо проверить, были ли созданы параметры каталога для этой функции, и если нет, использовать значения из соответствующего шаблона параметров каталога.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. Изучите значения параметров. Есть два значения параметров, которые определяют, какие пользователи смогут предоставлять приложению доступ к данным своей группы:

    | Параметр       | Тип         | Описание  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Логическое | Флаг, указывающий, разрешено ли владельцам групп предоставлять разрешения для конкретной группы. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Если параметр _EnableGroupSpecificConsent_ имеет значение "true", а для этого значения задан идентификатор объекта группы, члены этой группы будут иметь права на предоставление разрешений, относящихся к их группе. |

1. Обновите значения параметров для требуемой конфигурации:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. Сохраните параметры.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>Настройка повышения уровня согласия на основе рисков

Повышение уровня согласия на основе рисков позволяет снизить уязвимость пользователей для вредоносных приложений, которые делают [незаконные запросы на согласие](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Если корпорация Майкрософт обнаружит рискованный запрос согласия пользователя, запрос будет перенаправлен администратору. Эта возможность включена по умолчанию, но поведение будет изменено только при включенном согласии конечных пользователей.

При обнаружении рискованного запроса на согласие будет отображаться сообщение о необходимости утверждения администратором. Если [рабочий процесс запроса согласия администратора](configure-admin-consent-workflow.md) включен, пользователь может отправить запрос администратору для дальнейшей проверки непосредственно из запроса согласия. Если он не включен, отображается следующее сообщение:

* **AADSTS90094:** &lt;clientAppDisplayName&gt; требуются разрешения на доступ к ресурсам в вашей организации, которые может предоставить только администратор. Попросите администратора предоставить разрешение для этого приложения, прежде чем его можно будет использовать.

В этом случае событие аудита также будет регистрироваться с категорией "ApplicationManagement", типом действия "Согласие на приложение" и состоянием "Обнаружено рискованное приложение".

> [!IMPORTANT]
> Администраторы должны внимательно [изучать все запросы на согласие](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) перед утверждением запроса, особенно если корпорация Майкрософт обнаружила риск.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Отключение и повторное включение повышения уровня согласия на основе риска с помощью PowerShell

Вы можете использовать модуль предварительной версии Azure AD PowerShell, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview), чтобы отключить перенаправление согласия администратору в случаях, когда корпорация Майкрософт обнаружит риск, или повторно включить его, если оно было отключено.

Это можно сделать так же, как [вы настраивали согласие владельца группы с помощью PowerShell](#configure-group-owner-consent-using-powershell), но подставив другие значения параметров. Есть три различия: 

1. Изучите значения параметров для повышения уровня согласия на основе рисков:

    | Параметр       | Тип         | Описание  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Логическое |  Флаг, указывающий, будет ли согласие пользователя блокироваться при обнаружении рискованного запроса. |

1. Замените следующее значение на шаге 3:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. Замените одно из следующих значений на шаге 5:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы получить дополнительные сведения, обратитесь к разделу

* [Настройка рабочего процесса согласия администратора](configure-admin-consent-workflow.md)
* [Узнайте, как управлять согласием для приложений и оценивать запросы на согласие](manage-consent-requests.md)
* [Предоставление приложению согласия администратора на уровне арендатора](grant-admin-consent.md)
* [Разрешения и согласие для платформы удостоверений Майкрософт](../develop/active-directory-v2-scopes.md)

Получение справки или ответов на вопросы:
* [Azure AD в StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
