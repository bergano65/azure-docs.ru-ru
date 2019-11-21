---
title: Microsoft Graph API for Azure Active Directory Identity Protection
description: Learn how to query Microsoft Graph risk detections and associated information from Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3c7d2c6fe5a489415103a4da5daf707f9585f9d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212881"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Начало работы с защитой идентификации Azure Active Directory и Microsoft Graph

Microsoft Graph — это конечная точка Unified API (Майкрософт) и источник интерфейсов API [защиты идентификации Azure Active Directory](../active-directory-identityprotection.md). There are four APIs that expose information about risky users and sign-ins. The first API, **riskDetection**, allows you to query Microsoft Graph for a list of both user and sign-in linked risk detections and associated information about the detection. Второй API, **riskyUsers**, позволяет запрашивать у Microsoft Graph информацию о пользователях, которых служба "Защита идентификации" определила как выполняющих рискованные действия. Третий API, **signIn**, позволяет запрашивать у Microsoft Graph информацию о входах в Azure AD со специфическими свойствами, связанными с состоянием, подробностями и уровнем риска. The fourth API, **identityRiskEvents**, allows you to query Microsoft Graph for a list of [risk detections](../reports-monitoring/concept-risk-events.md) and associated information. This article gets you started with connecting to the Microsoft Graph and querying these APIs. Дополнительные сведения, полную документацию и доступ к Graph Explorer можно получить на [сайте Microsoft Graph](https://graph.microsoft.io/) или по следующим ссылкам на документацию по API-интерфейсам:

* [riskDetection API](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)
* [API riskyUsers](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [API signIn](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)
* [API identityRiskEvents](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)

## <a name="connect-to-microsoft-graph"></a>Подключение к Microsoft Graph

Получить доступ к данным защиты идентификации с помощью Microsoft Graph можно в четыре этапа:

1. Получение имени домена.
2. Создание регистрации приложения. 
3. Использование этого секрета и других данных для прохождения проверки подлинности в Microsoft Graph и получения маркера проверки подлинности. 
4. Использование полученного маркера для отправки запросов к конечной точке API и получения данных защиты идентификации.

Перед началом работы вам потребуются:

* права администратора для создания приложения в Azure AD;
* имя домена клиента (например, contoso.onmicrosoft.com).

## <a name="retrieve-your-domain-name"></a>Получение имени домена 

1. [Войдите](https://portal.azure.com) на портал Azure с учетной записью администратора. 
1. В области навигации слева щелкните **Active Directory**. 

   ![Создание приложения](./media/howto-identity-protection-graph-api/41.png)

1. В разделе **Управление** щелкните **Свойства**.

   ![Создание приложения](./media/howto-identity-protection-graph-api/42.png)

1. Скопируйте имя домена.

## <a name="create-a-new-app-registration"></a>Создание регистрации приложения

1. На странице **Active Directory** в разделе **Управление** щелкните **Регистрация приложений**.

   ![Создание приложения](./media/howto-identity-protection-graph-api/42.png)

1. В меню в верхней части страницы щелкните **Регистрация нового приложения**.

   ![Создание приложения](./media/howto-identity-protection-graph-api/43.png)

1. На странице **Создание** выполните следующие действия.

   ![Создание приложения](./media/howto-identity-protection-graph-api/44.png)

   1. In the **Name** textbox, type a name for your application (for example: Azure AD Risk Detection API Application).

   1. В качестве **типа** выберите **Веб-приложение и/или веб-API**.

   1. В текстовом поле **URL-адрес входа** введите `http://localhost`.

   1. Щелкните **Create**(Создать).
1. Чтобы открыть страницу **Параметры**, в списке приложений щелкните только что созданную регистрацию приложения. 
1. Скопируйте **идентификатор приложения**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Предоставление приложению разрешения на использование API

1. На странице **Параметры** щелкните **Необходимые разрешения**.

   ![Создание приложения](./media/howto-identity-protection-graph-api/15.png)

1. На странице **Необходимые разрешения** на панели инструментов вверху нажмите кнопку **Добавить**.

   ![Создание приложения](./media/howto-identity-protection-graph-api/16.png)

1. На странице **Добавить доступ через API** щелкните **Выбор API**.

   ![Создание приложения](./media/howto-identity-protection-graph-api/17.png)

1. На странице **Выбор API** выберите **Microsoft Graph**, а затем нажмите кнопку **Выбор**.

   ![Создание приложения](./media/howto-identity-protection-graph-api/18.png)

1. На странице **Добавить доступ через API** щелкните **Выбрать разрешения**.

   ![Создание приложения](./media/howto-identity-protection-graph-api/19.png)

1. На странице **Включение доступа** щелкните **Read all identity risk information** (Прочитать все сведения о рисках идентификаторов), а затем щелкните **Выбор**.

   ![Создание приложения](./media/howto-identity-protection-graph-api/20.png)

1. На странице **Добавить доступ через API** щелкните **Готово**.

   ![Создание приложения](./media/howto-identity-protection-graph-api/21.png)

1. На странице **Требуемые разрешения** щелкните **Предоставить разрешения** и **Да**.

   ![Создание приложения](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Получение ключа доступа

1. На странице **Параметры** щелкните **Ключи**.

   ![Создание приложения](./media/howto-identity-protection-graph-api/23.png)

1. На странице **Ключи** выполните следующие действия.

   ![Создание приложения](./media/howto-identity-protection-graph-api/24.png)

   1. In the **Key description** textbox, type a description (for example, *Azure AD Risk Detection*).
   1. Для параметра **Длительность** выберите значение **Через один год**.
   1. В нижней части страницы нажмите кнопку **Save**.
   1. Скопируйте значение ключа и вставьте его в безопасное место.   
   
   > [!NOTE]
   > Если ключ будет утерян, вам нужно будет вернуться в этот раздел и создать новый ключ. Не предоставляйте этот ключ никому: с его помощью кто угодно может получить доступ к вашим данным.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Authenticate to Microsoft Graph and query the Identity Risk Detections API

На этом этапе вам понадобятся:

- имя домена клиента.
- идентификатор клиента; 
- ключ. 

Чтобы выполнить проверку подлинности, отправьте запрос POST по адресу `https://login.microsoft.com` со следующими параметрами в тексте:

- grant_type: “**client_credentials**”
- resource: `https://graph.microsoft.com`
- client_id: \<ваш идентификатор клиента\>;
- client_secret: \<ваш ключ\>.

В случае успешного выполнения этот метод возвращает маркер проверки подлинности.  
Для вызова API создайте заголовок со следующим параметром:

```
`Authorization`="<token_type> <access_token>"
```

При проверке подлинности тип маркера и маркер доступа можно найти в возвращаемом маркере.

Отправьте этот заголовок как запрос по следующему URL-адресу API: `https://graph.microsoft.com/beta/identityRiskEvents`

The response, if successful, is a collection of identity risk detections and associated data in the OData JSON format, which can be parsed and handled as you see fit.

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

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
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

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Get all of the offline risk detections (riskDetection API)

With Identity Protection sign-in risk policies, you can apply conditions when risk is detected in real time. But what about detections that are discovered offline? To understand what detections occurred offline, and thus would not have triggered the sign-in risk policy, you can query the riskDetection API.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-the-high-risk-and-medium-risk-detections-identityriskevents-api"></a>Get the high-risk and medium-risk detections (identityRiskEvents API)

Medium and high-risk detections represent those that may have the capability to trigger Identity Protection sign-in or user-risk policies. Исправление этих событий должно быть приоритетным, так как они имеют среднюю или высокую вероятность того, что пользователь, пытающийся войти в систему, не является законным владельцем удостоверения. 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Получение списка всех пользователей, успешно прошедших многофакторную проверку подлинности, активированную политикой рискованных входов (API riskyUsers)

To understand the impact Identity Protection risk-based policies have on your organization, you can query all of the users who successfully passed an MFA challenge triggered by a risky sign-ins policy. Эта информация поможет понять, каких пользователей Защита идентификации может ошибочно определить как рискованных, а также какие из ваших уполномоченных пользователей могут выполнять действия, которые искусственный интеллект считает рискованными.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Получение списка всех рискованных входов конкретного пользователя (API signIn)

Если вы полагаете, что пользователь может быть скомпрометирован, можно понять состояние риска, получив список всех рискованных входов в систему, выполненных этим пользователем. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Дальнейшие действия

Поздравляем, вы только что выполнили первый вызов Microsoft Graph.  
Now you can query identity risk detections and use the data however you see fit.

Дополнительные сведения о Microsoft Graph и инструкции по созданию приложения с помощью API Graph см. в [документации](https://docs.microsoft.com/graph/overview), а также на [веб-сайте Microsoft Graph](https://developer.microsoft.com/graph). 

Связанные сведения:

- [Защита идентификации Azure Active Directory.](../active-directory-identityprotection.md)
- [Types of risk detections detected by Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Overview of Microsoft Graph (Обзор Microsoft Graph)](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection Service Root (Корень службы защиты идентификации Azure AD)](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
