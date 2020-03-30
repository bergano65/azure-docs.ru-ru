---
title: Записи аудита доступа и аудита
titleSuffix: Azure AD B2C
description: Как получить доступ к журналам аудита Azure AD B2C программно и на портале Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 83086fa2cb96eba423b9111134a0406d7256821f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264223"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Обращение к журналам аудита Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) излучает журналы аудита, содержащие сведения о действиях о ресурсах B2C, выпущенных токенах и доступе администратора. В этой статье содержится краткий обзор информации, доступной в журналах аудита, и инструкции о том, как получить доступ к этим данным для арендатора Azure AD B2C.

События журнала аудита сохраняются только в течение **семи дней.** Если требуется более длительный срок хранения, спланируйте скачивание и хранение журналов с помощью одного из описанных ниже методов.

> [!NOTE]
> Вы не можете видеть входе пользователей для отдельных приложений Azure AD B2C в разделе **Пользователей** **Active Directory** или **Страницы Azure AD B2C** на портале Azure. События ввне показывают активность пользователя, но не могут быть связаны с приложением B2C, в которое вписался пользователь. Для этого необходимо использовать журналы аудита, как поясняется далее в этой статье.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Общие сведения о действиях, доступных в категории B2C для журналов аудита

Категория **B2C** в журналах аудита содержит следующие типы действий:

|тип действия; |Описание  |
|---------|---------|
|Авторизация |Мероприятия, связанные с разрешением пользователю получить доступ к ресурсам B2C (например, администратору, доступу к списку политик B2C).         |
|Каталог |Действия, связанные с атрибутами каталога, извлекаемые при попавании администратора в использовании портала Azure. |
|Приложение | Создавайте, считывайте, обновляйте и удаляйте (CRUD) операции в приложениях B2C. |
|Ключ |ОПЕРАЦИИ CRUD на ключах, хранящихся в контейнере клавиш B2C. |
|Ресурс |ОПЕРАЦИИ CRUD на ресурсах B2C. Например, политики и поставщики идентификационных данных.
|Проверка подлинности |Проверка учетных данных пользователей и выдача токенов.|

Сведения о действиях CRUD для объектов пользователя см. в категории **основного каталога**.

## <a name="example-activity"></a>Пример действия

На этом примере с портала Azure показаны данные, полученные при входе пользователя в службу внешнего поставщика идентификационных данных, в данном случае Facebook:

![Пример страницы сведения о деятельности журнала аудита на портале Azure](./media/view-audit-logs/audit-logs-example.png)

Панель сведений о деятельности содержит следующую соответствующую информацию:

|Section|Поле|Описание|
|-------|-----|-----------|
| Действие | name | Какая деятельность состоялась. Например, *выдать id_token в приложение,* которое завершает фактический пользовательский входе в систему. |
| "Кем инициировано (субъект)". | ObjectId | **Идентификатор объекта** приложения B2C, в которое входит пользователь. Этот идентификатор не виден на портале Azure, но доступен через API Microsoft Graph. |
| "Кем инициировано (субъект)". | Spn | **Идентификатор приложения** приложения B2C, в которое пользователь вписывается. |
| "Целевые объекты"; | ObjectId | **Идентификатор объекта** пользователя, вступовав в систему. |
| Дополнительные сведения | TenantId | **Идентификатор арендатора** для арендатора AD B2C Azure. |
| Дополнительные сведения | PolicyId | **Идентификатор политики** потока пользователя (политики), используемый для входиный запрос пользователя. |
| Дополнительные сведения | ApplicationId | **Идентификатор приложения** приложения B2C, в которое пользователь вписывается. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Просмотр журналов аудита на портале Azure

Портал Azure предоставляет доступ к событиям журнала аудита в вашем арендаторе Azure AD B2C.

1. Войти на [портал Azure](https://portal.azure.com)
1. Переключитесь на каталог, содержащий арендатор Azure AD B2C, а затем просмотрите на **Azure AD B2C.**
1. В **рамках мероприятий** в левом меню выберите **журналы аудита**.

Отображается список событий активности, зарегистрированных за последние семь дней.

![Пример фильтра с двумя событиями активности на портале Azure](./media/view-audit-logs/audit-logs-example-filter.png)

Доступно несколько вариантов фильтрации, в том числе:

* **Тип ресурсов активности** - Фильтр по типам действий, показанным в таблице в [разделе Обзор доступных действий.](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)
* **Дата** - Фильтр диапазон даты действий показано.

При выборе строки в списке отображаются сведения о действиях для события.

Чтобы загрузить список событий активности в файле, разделенном на запятую (CSV), выберите **Download.**

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Получить журналы аудита с API aPI API

Журналы аудита публикуются в том же конвейере, что и другие действия для Azure Active Directory, поэтому к ним можно обратиться через [API отчетов Azure Active Directory](https://docs.microsoft.com/graph/api/directoryaudit-list). Для получения дополнительной информации смотрите начало [с API-извне Active Directory API.](../active-directory/reports-monitoring/concept-reporting-api.md)

### <a name="enable-reporting-api-access"></a>Включить доступ к API-сообщению

Для обеспечения доступа к API API API AD AD, основанному на скриптах или приложениях, необходимо приложение, зарегистрированное в вашем наемщике Azure AD B2C со следующими разрешениями API. Вы можете включить эти разрешения на существующую регистрацию приложений в вашем арендаторе B2C, или создать новый специально для использования с автоматизацией журналов аудита.

* Microsoft Graph > разрешения приложений > AuditLog > AuditLog.Read.All

Выполните следующие действия в следующей статье для регистрации приложения с требуемыми разрешениями:

[Управление Azure AD B2C с помощью графика Microsoft](microsoft-graph-get-started.md)

После регистрации приложения с соответствующими разрешениями смотрите раздел сценария PowerShell позже в этой статье, например, как можно получить события активности со сценарием.

### <a name="access-the-api"></a>Доступ к API

Чтобы загрузить события журналов аудита Azure AD B2C через `B2C` API, отфильтруйте журналы в категории. Чтобы фильтровать по `filter` категориям, используйте параметр строки запроса при вызове aPI-сообщения API.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Скрипт PowerShell

Следующий скрипт PowerShell показывает пример того, как задать запрос API отчетов Azure AD. После запроса API он печатает зарегистрированные события на стандартный вывод, а затем записывает выход JSON в файл.

Вы можете попробовать этот скрипт в [облачной оболочке Azure.](overview.md) Обязательно обновите его с помощью идентификатора приложения, секрета клиента и имени арендатора Azure AD B2C.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
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

Вот представление JSON о примере события действия, показанном ранее в статье:

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

## <a name="next-steps"></a>Дальнейшие действия

Можно автоматизировать другие задачи администрирования, например, [управлять учетными записями пользователей Azure AD B2C с помощью Microsoft Graph.](manage-user-accounts-graph-api.md)
