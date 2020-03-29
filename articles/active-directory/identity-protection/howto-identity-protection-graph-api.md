---
title: Microsoft Graph API для защиты от активной идентификации каталогов Azure
description: Узнайте, как заставить зазнать обнаружение рисков Microsoft Graph и связанную с ними информацию из каталога Azure Active
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
ms.openlocfilehash: f6fd62ccab4d6e32b23835d280732797e133ada8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671625"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Начало работы с защитой идентификации Azure Active Directory и Microsoft Graph

Microsoft Graph — это конечная точка Unified API (Майкрософт) и источник интерфейсов API [защиты идентификации Azure Active Directory](../active-directory-identityprotection.md). Есть четыре AA, которые разоблачают информацию о рискованных пользователях и ввозах. Первый API, **riskDetection,** позволяет запросить Microsoft Graph для списка как пользователей, так и связанных с ними обнаружений рисков и связанной с ними информации об обнаружении. Второй API, **riskyUsers**, позволяет запрашивать у Microsoft Graph информацию о пользователях, которых служба "Защита идентификации" определила как выполняющих рискованные действия. Третий API, **signIn**, позволяет запрашивать у Microsoft Graph информацию о входах в Azure AD со специфическими свойствами, связанными с состоянием, подробностями и уровнем риска. Четвертый API, **identityRiskEvents**, позволяет запросить Microsoft Graph для списка [обнаружений рисков](../reports-monitoring/concept-risk-events.md) и связанной с ними информации. API identityRiskEvents будет унесена 10 января 2020 года; мы предлагаем вам использовать API **riskDetections** вместо. Эта статья знакомит вас с подключением к Microsoft Graph и запросами к этим API-интерфейсам. Дополнительные сведения, полную документацию и доступ к Graph Explorer можно получить на [сайте Microsoft Graph](https://graph.microsoft.io/) или по следующим ссылкам на документацию по API-интерфейсам:

* [РискОбнаружения API](/graph/api/resources/riskdetection?view=graph-rest-beta)
* [API riskyUsers](/graph/api/resources/riskyuser?view=graph-rest-beta)
* [API signIn](/graph/api/resources/signin?view=graph-rest-beta)
* [identityRiskEvents API](/graph/api/resources/identityriskevent?view=graph-rest-beta) *будет обесточен 10 января 2020*

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

   1. В текстовом ящике **Name** введите имя приложения (например: Приложение API обнаружения рисков Azure AD).

   1. В качестве **типа** выберите **Веб-приложение и/или веб-API**.

   1. В текстовом ящике Для `http://localhost` **ввода— введите** — введите —

   1. Нажмите кнопку **Создать**.
1. Чтобы открыть страницу **Параметры**, в списке приложений щелкните только что созданную регистрацию приложения. 
1. Копирование **идентификатора приложения**.

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

   1. В текстовом ящике **описания ключей** введите описание (например, *Обнаружение рисков Azure AD).*
   1. Для параметра **Длительность** выберите значение **Через один год**.
   1. Нажмите **Сохранить**.
   1. Скопируйте значение ключа и вставьте его в безопасное место.   
   
   > [!NOTE]
   > Если ключ будет утерян, вам нужно будет вернуться в этот раздел и создать новый ключ. Не предоставляйте этот ключ никому: с его помощью кто угодно может получить доступ к вашим данным.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Authenticate to Microsoft Graph и запрос API обнаружения рисков идентификации

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

Ответ, в случае успеха, представляет собой набор обнаружений рисков идентификации и связанных с ними данных в формате OData JSON, которые могут быть разобрана и обработаны, как вы считаете нужным.

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

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Получите все обнаружения риска в автономном режиме (RiskDetection API)

С помощью политики риска идентификационных данных можно применять условия при обнаружении риска в режиме реального времени. Но как насчет обнаружения, которые обнаружены в автономном режиме? Чтобы понять, какие обнаружения произошли в автономном режиме, и, таким образом, не вызвали бы политику риска ввне, можно задать запрос API обнаружения риска.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Получение списка всех пользователей, успешно прошедших многофакторную проверку подлинности, активированную политикой рискованных входов (API riskyUsers)

Чтобы понять влияние политики, основанные на риске Identity Protection, на вашу организацию, можно задать вопрос всем пользователям, успешно пройдевшим задачу MFA, вызванную рискованной политикой вхасывания. Эта информация поможет понять, каких пользователей Защита идентификации может ошибочно определить как рискованных, а также какие из ваших уполномоченных пользователей могут выполнять действия, которые искусственный интеллект считает рискованными.

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
Теперь вы можете зазнать обнаружения рисков идентификации и использовать данные, как вы считаете нужным.

Дополнительные сведения о Microsoft Graph и инструкции по созданию приложения с помощью API Graph см. в [документации](/graph/overview), а также на [веб-сайте Microsoft Graph](https://developer.microsoft.com/graph). 

Связанные сведения:

- [Защита идентификации Azure Active Directory](../active-directory-identityprotection.md)
- [Типы обнаружений рисков, обнаруженные в Active Directory Protection Azure](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Overview of Microsoft Graph (Обзор Microsoft Graph)](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection Service Root (Корень службы защиты идентификации Azure AD)](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
