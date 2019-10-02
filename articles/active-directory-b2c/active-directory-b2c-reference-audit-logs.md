---
title: Примеры и определения журналов аудита в Azure Active Directory B2C
description: Руководства и примеры по доступу к журналам аудита Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: bf9b6a3ad40d46b628bfcdb3fa3e32b2419360c9
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802112"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Обращение к журналам аудита Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) выдает журналы аудита, содержащие сведения о действиях ресурсов B2C, выданных токенов и доступе администратора. Эта статья содержит краткий обзор сведений, доступных в журналах аудита, и инструкции по получению доступа к этим данным для клиента Azure AD B2C.

События журнала аудита хранятся только в течение **семи дней**. Если требуется более длительный срок хранения, спланируйте скачивание и хранение журналов с помощью одного из описанных ниже методов.

> [!NOTE]
> Вы не видите возможность входа пользователей для отдельных Azure AD B2C приложений в разделе " **Пользователи** " страницы **Azure Active Directory** или **Azure AD B2C** на портал Azure. События входа отображают действия пользователя, но не могут быть взаимосвязаны с приложением B2C, в которое пользователь вошел в. Для этого необходимо использовать журналы аудита, как описано далее в этой статье.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Общие сведения о действиях, доступных в категории B2C для журналов аудита

Категория **B2C** в журналах аудита содержит следующие типы действий:

|Тип действия |Описание  |
|---------|---------|
|Authorization |Действия, касающиеся авторизации пользователя для доступа к ресурсам B2C (например, администратор, обращающийся к списку политик B2C).         |
|Каталог |Действия, связанные с атрибутами каталога, полученными при входе администратора с помощью портал Azure. |
|Приложение | Операции создания, чтения, обновления и удаления (CRUD) в приложениях B2C. |
|Ключ |Операции CRUD с ключами, хранящимися в контейнере ключей B2C. |
|Resource |Операции CRUD с ресурсами B2C. Например, политики и поставщики удостоверений.
|Проверка подлинности |Проверка учетных данных пользователя и выдачи маркера.|

Сведения о действиях CRUD для объектов пользователя см. в категории **основного каталога**.

## <a name="example-activity"></a>Пример действия

В этом примере изображения из портал Azure показаны данные, захваченные при входе пользователя с помощью внешнего поставщика удостоверений, в данном случае Facebook:

![Пример страницы сведений об активности журнала аудита в портал Azure](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

Панель сведения об активности содержит следующие важные сведения.

|`Section`|Поле|Описание|
|-------|-----|-----------|
| Действия | Название | Какое действие выполнялось. Например, *выдайте приложению id_token*, которое завершает фактический вход пользователя. |
| "Кем инициировано (субъект)". | ObjectId | **Идентификатор объекта** приложения B2C, в котором выполняется вход пользователя. Этот идентификатор недоступен в портал Azure, но доступен через API Microsoft Graph. |
| "Кем инициировано (субъект)". | Имя субъекта-службы | **Идентификатор** приложения B2C, в котором выполняется вход пользователя. |
| Целевые объекты | ObjectId | **Идентификатор объекта** пользователя, который подписывается. |
| Дополнительные сведения | TenantId | **Идентификатор клиента** Azure AD B2C клиента. |
| Дополнительные сведения | `PolicyId` | **Идентификатор политики** потока пользователя (политики), используемого для входа пользователя. |
| Дополнительные сведения | ApplicationId | **Идентификатор** приложения B2C, в котором выполняется вход пользователя. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Просмотр журналов аудита в портал Azure

Портал Azure предоставляет доступ к событиям журнала аудита в клиенте Azure AD B2C.

1. Войдите на [портал Azure](https://portal.azure.com)
1. Перейдите в каталог, содержащий клиент Azure AD B2C, а затем перейдите к **Azure AD B2C**.
1. В разделе **действия** в меню слева выберите **журналы аудита**.

Отобразится список событий действий, регистрируемых за последние семь дней.

![Пример фильтра с двумя событиями действий в портал Azure](media/active-directory-b2c-reference-audit-logs/audit-logs-example-filter.png)

Доступно несколько вариантов фильтрации, в том числе:

* **Тип ресурса действия** — фильтрация по типам действий, показанным в таблице в разделе [Обзор доступных действий](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) .
* **Дата** — фильтрация диапазона дат для отображаемых действий.

Если выбрать строку в списке, отобразятся сведения о действии для события.

Чтобы скачать список событий действий в файле с разделителями-запятыми (CSV), выберите **скачать**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Получение журналов аудита с помощью API отчетов Azure AD

Журналы аудита публикуются в том же конвейере, что и другие действия для Azure Active Directory, поэтому к ним можно обратиться через [API отчетов Azure Active Directory](https://docs.microsoft.com/graph/api/directoryaudit-list). Дополнительные сведения см. [в статье Приступая к работе с API отчетов Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Включить доступ к API отчетов

Чтобы разрешить доступ на основе скриптов или приложений к API отчетов Azure AD, вам потребуется приложение Azure Active Directory, зарегистрированное в клиенте Azure AD B2C со следующими разрешениями API:

* Microsoft Graph
  * Приклад Прочитать все данные журнала аудита

Эти разрешения можно включить для существующей регистрации Azure Active Directory приложения в клиенте B2C или создать новую, специально для использования с автоматизацией журналов аудита.

Выполните следующие действия, чтобы зарегистрировать приложение, предоставить ему необходимые разрешения Microsoft Graph API, а затем создать секрет клиента.

### <a name="register-application-in-azure-active-directory"></a>Регистрация приложения в Azure Active Directory

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>Назначение разрешений доступа API

1. На странице Обзор **зарегистрированного приложения** выберите **Параметры**.
1. В разделе **доступ через API**выберите **необходимые разрешения**.
1. Нажмите кнопку **Добавить**, а затем **выберите API**.
1. Выберите **Microsoft Graph**, а затем **выберите**.
1. В разделе **разрешения приложения**выберите **чтение всех данных журнала аудита**.
1. Нажмите кнопку **выбрать** и выберите **Готово**.
1. Щелкните **Предоставить разрешения** и затем выберите **Да**.

### <a name="create-client-secret"></a>Создать секрет клиента

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Теперь у вас есть приложение с требуемым доступом API, ИДЕНТИФИКАТОРом приложения и ключом, которые можно использовать в скриптах автоматизации. Пример того, как можно получить события действий с помощью скрипта, см. в разделе сценарий PowerShell далее в этой статье.

### <a name="access-the-api"></a>Доступ к API

Чтобы скачать события журнала аудита Azure AD B2C с помощью API, отфильтруйте журналы в `B2C` категории. Для фильтрации по категории используйте `filter` параметр строки запроса при вызове конечной точки API отчетов Azure AD.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Сценарий PowerShell

В следующем сценарии PowerShell показан пример запроса к API отчетов Azure AD. После запроса к API-интерфейсу он выводит записанные события в стандартный вывод, а затем записывает выходные данные JSON в файл.

Вы можете попробовать этот скрипт в [Azure Cloud Shell](../cloud-shell/overview.md). Обязательно обновите его с помощью идентификатора приложения, ключа и имени клиента Azure AD B2C.

```powershell
# This script requires the registration of a Web Application in Azure Active Directory:
# https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a GUID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client secret/key
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant; for example, contoso.onmicrosoft.com
$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Ниже приведено представление JSON примера события действия, показанного ранее в статье:

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Следующие шаги

Можно автоматизировать другие задачи администрирования, например [управлять пользователями с помощью .NET](active-directory-b2c-devquickstarts-graph-dotnet.md).
