---
title: Microsoft Graph API для Защита идентификации Azure Active Directory
description: Узнайте, как запрашивать Microsoft Graph обнаружения рисков и связанные сведения из Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d88f841f76b367e83f0ae6b81e604e1b7f3e4b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950125"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Начало работы с защитой идентификации Azure Active Directory и Microsoft Graph

Microsoft Graph — это конечная точка Unified API (Майкрософт) и источник интерфейсов API [защиты идентификации Azure Active Directory](./overview-identity-protection.md). Существует три интерфейса API, которые предоставляют сведения о рискованных пользователях и входах. Первый интерфейс API, **рискдетектион**, позволяет запрашивать Microsoft Graph для получения списка обнаруженных рисков, связанных с входом пользователя и входа, и связанных сведений об обнаружении. Второй API, **riskyUsers**, позволяет запрашивать у Microsoft Graph информацию о пользователях, которых служба "Защита идентификации" определила как выполняющих рискованные действия. Третий API, **signIn**, позволяет запрашивать у Microsoft Graph информацию о входах в Azure AD со специфическими свойствами, связанными с состоянием, подробностями и уровнем риска. 

Эта статья знакомит вас с подключением к Microsoft Graph и запросами к этим API-интерфейсам. Дополнительные сведения, полную документацию и доступ к Graph Explorer можно получить на [сайте Microsoft Graph](https://graph.microsoft.io/) или по следующим ссылкам на документацию по API-интерфейсам:

* [API riskDetection](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [API riskyUsers](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [API signIn](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>Подключение к Microsoft Graph

Получить доступ к данным защиты идентификации с помощью Microsoft Graph можно в четыре этапа:

- [Получение имени домена](#retrieve-your-domain-name)
- [Создание регистрации приложения](#create-a-new-app-registration)
- [Настройка разрешений API](#configure-api-permissions)
- [Настройка допустимых учетных данных](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>Получение имени домена 

1. Войдите на [портал Azure](https://portal.azure.com).  
1. Перейдите к **Azure Active Directory**  >  **пользовательских доменных имен**. 
1. Запишите `.onmicrosoft.com` домен, эти сведения понадобятся вам позже.

### <a name="create-a-new-app-registration"></a>Создание регистрации приложения

1. В портал Azure перейдите к **Azure Active Directory**  >  **Регистрация приложений**.
1. Выберите **Новая регистрация**.
1. На странице **Создание** выполните следующие действия.
   1. В текстовом поле **имя** введите имя приложения (например, API обнаружения рисков Azure AD).
   1. В разделе **Поддерживаемые типы учетных записей**выберите тип учетных записей, которые будут использовать API.
   1. Выберите **Зарегистрировать**.
1. Скопируйте **идентификатор приложения**.

### <a name="configure-api-permissions"></a>Настройка разрешений API

1. В созданном **приложении** выберите **разрешения API**.
1. На странице **настроенные разрешения** на панели инструментов в верхней части страницы щелкните **Добавить разрешение**.
1. На странице **Добавить доступ через API** щелкните **Выбор API**.
1. На странице **Выбор API** выберите **Microsoft Graph**, а затем нажмите кнопку **Выбор**.
1. На странице **разрешения API запроса** выполните следующие действия. 
   1. Выберите **Разрешения приложений**.
   1. Установите флажки рядом с `IdentityRiskEvent.Read.All` и `IdentityRiskyUser.Read.All` .
   1. Выберите **Добавить разрешения**.
1. Выберите параметр **предоставить согласие администратора для домена** . 

### <a name="configure-a-valid-credential"></a>Настройка допустимых учетных данных

1. В созданном **приложении** выберите **Сертификаты & секреты**.
1. В разделе **Секреты клиента** выберите **Новый секрет клиента**.
   1. Введите **Описание** секрета клиента и задайте период времени окончания срока действия согласно политикам организации.
   1. Выберите **Добавить**.

   > [!NOTE]
   > Если ключ будет утерян, вам нужно будет вернуться в этот раздел и создать новый ключ. Не предоставляйте этот ключ никому: с его помощью кто угодно может получить доступ к вашим данным.

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Проверка подлинности на Microsoft Graph и запрос API обнаружения рисков удостоверений

На этом этапе вам понадобятся:

- имя домена клиента.
- Идентификатор приложения (клиента); 
- Секрет клиента или сертификат 

Чтобы выполнить проверку подлинности, отправьте запрос POST по адресу `https://login.microsoft.com` со следующими параметрами в тексте:

- grant_type: “**client_credentials**”
- resource: `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

В случае успеха этот запрос возвращает маркер проверки подлинности.  
Для вызова API создайте заголовок со следующим параметром:

```
`Authorization`="<token_type> <access_token>"
```

При проверке подлинности тип маркера и маркер доступа можно найти в возвращаемом маркере.

Отправьте этот заголовок как запрос по следующему URL-адресу API: `https://graph.microsoft.com/v1.0/identityProtection/riskDetections`

Ответ, если он был успешным, представляет собой коллекцию обнаружений рисков идентификации и связанных данных в формате OData JSON, который можно проанализировать и обработать по своему усмотрению.

### <a name="sample"></a>Пример

Ниже приведен пример кода для аутентификации и вызова API с помощью PowerShell.  
Просто добавьте свои идентификатор клиента, секретный ключ и домен клиента.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Запросы к API-интерфейсам

Эти три API-интерфейса предоставляют множество возможностей для получения информации о пользователях и входах в вашу организацию, связанных с рискованными действиями. Ниже приведены некоторые варианты использования этих API и связанные примеры запросов. Вы можете выполнить эти запросы, используя приведенный выше пример кода или [песочницу Graph](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Получение всех неавтономных обнаружений рисков (API Рискдетектион)

С помощью политик риска входа в систему защиты идентификации можно применять условия при обнаружении риска в режиме реального времени. Но как насчет обнаружений, обнаруживаемых вне сети? Чтобы понять, какие обнаружения обнаружены в автономном режиме и, таким образом, не активировали политику риска входа, можно запросить API Рискдетектион.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Получение списка всех пользователей, успешно прошедших многофакторную проверку подлинности, активированную политикой рискованных входов (API riskyUsers)

Чтобы понять, какие политики на основе риска для защиты идентификации влияют на работу в вашей организации, можно запросить всех пользователей, которые успешно прошли запрос MFA, активированный политикой входа в систему. Эта информация поможет понять, каких пользователей Защита идентификации может ошибочно определить как рискованных, а также какие из ваших уполномоченных пользователей могут выполнять действия, которые искусственный интеллект считает рискованными.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>Следующие шаги

Поздравляем, вы только что выполнили первый вызов Microsoft Graph.  
Теперь вы можете запросить обнаружение рисков удостоверений и использовать данные, но вы увидите их по своему усмотрению.

Дополнительные сведения о Microsoft Graph и инструкции по созданию приложения с помощью API Graph см. в [документации](/graph/overview), а также на [веб-сайте Microsoft Graph](https://developer.microsoft.com/graph). 

Связанные сведения:

- [Защита идентификации Azure Active Directory](./overview-identity-protection.md)
- [Типы обнаружений рисков, обнаруживаемые Защита идентификации Azure Active Directory](./overview-identity-protection.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Overview of Microsoft Graph (Обзор Microsoft Graph)](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection Service Root (Корень службы защиты идентификации Azure AD)](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)