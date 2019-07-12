---
title: Примеры и определения журналов аудита в Azure Active Directory B2C | Документация Майкрософт
description: Руководство и примеры по обращению к журналам аудита Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 216f5413ce3dae1f2d040643a30a4d7db4a879b8
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835415"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Обращение к журналам аудита Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) создает журналы аудита, содержащие сведения о действиях для ресурсов B2C, выданных токенах и доступе с правами администратора. Эта статья содержит краткий обзор данных, предоставляемых через журналы аудита, и инструкции о том, как получить доступ к этим данным для вашего клиента Azure AD B2C.

> [!IMPORTANT]
> Журналы аудита хранятся всего семь дней. Если требуется более длительный срок хранения, спланируйте скачивание и хранение журналов с помощью одного из описанных ниже методов.

> [!NOTE]
> Вы не видите входа пользователя в систему для отдельных приложений Azure AD B2C, в разделе **пользователей** раздел **Azure Active Directory** или **Azure AD B2C** колонок. Входов в систему будет отображаться действий пользователей, но не удается выполнить корреляцию в приложение B2C, который пользователь выполнил вход. Необходимо использовать журналы аудита для этого, как описано в этой статье.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Общие сведения о действиях, доступных в категории B2C для журналов аудита
Категория **B2C** в журналах аудита содержит следующие типы действий:

|тип действия; |Описание  |
|---------|---------|
|Authorization |Действия, касающиеся авторизации пользователя для доступа к ресурсам B2C (например, доступ администратора к списку политик B2C)         |
|Каталог |Действия, связанные с атрибутов каталога, получены, когда администратор выполняет вход с помощью портала Azure |
|Приложение | Операции CRUD для приложений B2C |
|Ключ |Операции CRUD для ключей, хранящихся в контейнере ключей B2C |
|Resource |Операции CRUD для ресурсов B2C (например, политик и поставщиков удостоверений)
|Проверка подлинности |Проверка учетных данных пользователя и выдача токенов|

> [!NOTE]
> Сведения о действиях CRUD для объектов пользователя см. в категории **основного каталога**.

## <a name="example-activity"></a>Пример действия
Приведенный ниже пример показывает данные, полученные при входе пользователя с помощью внешнего поставщика удостоверений: ![Пример страницы сведения об активности журналов аудита на портале Azure](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

Панель подробностей действий содержит следующие сведения:

|Раздел|Поле|Описание|
|-------|-----|-----------|
| Действие | ИМЯ | Какие действия были внесены. Например, «выдавать маркер "id_token" приложение» (который завершает вход пользователя). |
| "Кем инициировано (субъект)". | ObjectId | **Идентификатор объекта** приложения B2C, пользователь выполняет вход с (этот идентификатор не отображается на портале Azure, но он доступен через Graph API для примера). |
| "Кем инициировано (субъект)". | Имя участника-службы | **Идентификатор приложения** приложения B2C, пользователь выполняет вход с. |
| Целевые объекты | ObjectId | **Идентификатор объекта** пользователя, который выполняет вход. |
| Дополнительные сведения | TenantId | **Арендатора** клиента Azure AD B2C. |
| Дополнительные сведения | `PolicyId` | **Идентификатор политики** потока пользователя (политика), используемого для входа пользователя. |
| Дополнительные сведения | ApplicationId | **Идентификатор приложения** приложения B2C, пользователь выполняет вход с. |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Доступ к журналам аудита на портале Azure
1. Перейдите на [портал Azure](https://portal.azure.com). Перейдите в каталог B2C.
2. Щелкните **Azure Active Directory** на панели "Избранное" слева.

    ![Azure Active Directory выделена кнопка "в левом меню на портале](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. В разделе **Действия** щелкните **Журналы аудита**

    ![В разделе "действия" меню кнопку журналы аудита](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. В раскрывающемся списке **Категория** выберите **B2C**.
3. Нажмите кнопку **Применить**.

    ![Категории и кнопки "Применить", выделенным в фильтре журнал аудита](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Вы увидите список действий, внесенных в журнал за последние семь дней.
- Используйте раскрывающийся список **Тип ресурса действия** для фильтрации указанных выше типов действий.
- Используйте раскрывающийся список **Диапазон дат**, чтобы отфильтровать диапазон дат для указанных действий.
- Если щелкнуть определенную строку в списке, справа отображается контекстно-зависимое окно с дополнительными атрибутами, связанными с этим действием.
- Нажмите кнопку **Скачать**, чтобы скачать действия в виде CSV-файла.

> [!NOTE]
> Просмотреть журналы аудита можно также, перейдя по адресу **Azure AD B2C** вместо **Azure Active Directory** в панель «Избранное» в левой части. В разделе **действия**, щелкните **журналы аудита**, в котором вы увидите те же журналы, возможности которых аналогичны фильтрации.

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Доступ к журналам аудита через API отчетов Azure AD
Журналы аудита публикуются в том же конвейере, что и другие действия для Azure Active Directory, поэтому к ним можно обратиться через [API отчетов Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="prerequisites"></a>Предварительные требования
Чтобы проверить подлинность в API отчетов Azure AD, нужно сначала зарегистрировать приложение. Выполните действия из раздела [Предварительные требования для доступа к интерфейсам API отчетов Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

### <a name="accessing-the-api"></a>Получение доступа к API
Чтобы скачать журналы аудита Azure AD B2C через API, нужно отфильтровать журналы категории **B2C**. Для фильтрации по категории используйте параметр строки запроса при вызове конечной точки API отчетов Azure AD, как показано ниже:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>Сценарий PowerShell
Следующий сценарий показывает пример использования PowerShell для запроса API отчетов Azure AD и сохранения результатов в файле JSON:

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago

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
