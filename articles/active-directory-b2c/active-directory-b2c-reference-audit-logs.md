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
ms.openlocfilehash: d8cc67b8e243fb2b97cd1522a850adc63c84428e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69969629"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Обращение к журналам аудита Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) создает журналы аудита, содержащие сведения о действиях для ресурсов B2C, выданных токенах и доступе с правами администратора. Эта статья содержит краткий обзор данных, предоставляемых через журналы аудита, и инструкции о том, как получить доступ к этим данным для вашего клиента Azure AD B2C.

> [!IMPORTANT]
> Журналы аудита хранятся всего семь дней. Если требуется более длительный срок хранения, спланируйте скачивание и хранение журналов с помощью одного из описанных ниже методов.

> [!NOTE]
> Вы не можете увидеть пользовательские входы для отдельных Azure AD B2C приложений в разделе " **Пользователи** " в колонках **Azure Active Directory** или **Azure AD B2C** . При входе в систему будут отображаться действия пользователя, но их нельзя будет сопоставить с приложением B2C, в которое пользователь вошел в. Для этого необходимо использовать журналы аудита, как описано далее в этой статье.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Общие сведения о действиях, доступных в категории B2C для журналов аудита
Категория **B2C** в журналах аудита содержит следующие типы действий:

|Тип действия |Описание  |
|---------|---------|
|Authorization |Действия, касающиеся авторизации пользователя для доступа к ресурсам B2C (например, доступ администратора к списку политик B2C)         |
|Каталог |Действия, связанные с атрибутами каталога, полученными при входе администратора с помощью портал Azure |
|Приложение | Операции CRUD для приложений B2C |
|Ключ |Операции CRUD для ключей, хранящихся в контейнере ключей B2C |
|Resource |Операции CRUD для ресурсов B2C (например, политик и поставщиков удостоверений)
|Проверка подлинности |Проверка учетных данных пользователя и выдача токенов|

> [!NOTE]
> Сведения о действиях CRUD для объектов пользователя см. в категории **основного каталога**.

## <a name="example-activity"></a>Пример действия
Приведенный ниже пример показывает данные, полученные при входе пользователя с помощью внешнего поставщика удостоверений:  
    ![Пример страницы сведений об активности журнала аудита в портал Azure](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

Панель сведения об активности содержит следующие важные сведения.

|Раздел|Поле|Описание|
|-------|-----|-----------|
| Действия | Название | Какое действие выполнялось. Например, "дать id_token приложению" (которая завершает фактический вход пользователя в систему). |
| "Кем инициировано (субъект)". | ObjectId | **Идентификатор объекта** приложения B2C, в которое входит пользователь (этот идентификатор не отображается в портал Azure но доступен через API Graph например). |
| "Кем инициировано (субъект)". | Имя субъекта-службы | **Идентификатор** приложения B2C, в котором выполняется вход пользователя. |
| Целевые объекты | ObjectId | **Идентификатор объекта** пользователя, который подписывается. |
| Дополнительные сведения | TenantId | **Идентификатор клиента** Azure AD B2C клиента. |
| Дополнительные сведения | `PolicyId` | **Идентификатор политики** потока пользователя (политики), используемого для входа пользователя. |
| Дополнительные сведения | ApplicationId | **Идентификатор** приложения B2C, в котором выполняется вход пользователя. |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Доступ к журналам аудита с помощью портал Azure
1. Перейдите на [портал Azure](https://portal.azure.com). Перейдите в каталог B2C.
2. Щелкните **Azure Active Directory** на панели "Избранное" слева.

    ![Кнопка Azure Active Directory, выделенная в меню портала слева](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. В разделе **Действия** щелкните **Журналы аудита**

    ![Кнопка "журналы аудита", выделенная в разделе "действие" в меню](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. В раскрывающемся списке **Категория** выберите **B2C**.
3. Нажмите кнопку **Применить**.

    ![Категория и кнопка "Применить", выделенные в фильтре журнала аудита](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Вы увидите список действий, внесенных в журнал за последние семь дней.
- Используйте раскрывающийся список **Тип ресурса действия** для фильтрации указанных выше типов действий.
- Используйте раскрывающийся список **Диапазон дат**, чтобы отфильтровать диапазон дат для указанных действий.
- Если щелкнуть определенную строку в списке, справа отображается контекстно-зависимое окно с дополнительными атрибутами, связанными с этим действием.
- Нажмите кнопку **Скачать**, чтобы скачать действия в виде CSV-файла.

> [!NOTE]
> Журналы аудита также можно просмотреть, перейдя к **Azure AD B2C** , а не **Azure Active Directory** на панели Избранное слева. В разделе **действия**щелкните **журналы аудита**, где можно найти те же журналы с аналогичными возможностями фильтрации.

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
