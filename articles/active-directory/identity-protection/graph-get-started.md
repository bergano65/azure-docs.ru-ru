---
title: Использование Microsoft Graph для защиты идентификации Azure Active Directory | Документация Майкрософт
description: Узнайте, как выполнять запросы к Microsoft Graph для получения списка событий риска и связанных сведений из Azure Active Directory.
services: active-directory
keywords: защита идентификации azure active directory, события риска, уязвимость, политика безопасности, Microsoft Graph
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: sahandle
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3357cfd5e845346534f263c768b5cf6b6a38ea4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60296305"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Начало работы с защитой идентификации Azure Active Directory и Microsoft Graph

Microsoft Graph — это конечная точка Unified API (Майкрософт) и источник интерфейсов API [защиты идентификации Azure Active Directory](../active-directory-identityprotection.md). Существует три API, которые предоставляют информацию о пользователях и входах в систему, связанных с рискованными действиями. Первый API, **identityRiskEvents**, позволяет запрашивать у Microsoft Graph список [событий риска](../reports-monitoring/concept-risk-events.md) и связанные с ними сведения. Второй API, **riskyUsers**, позволяет запрашивать у Microsoft Graph информацию о пользователях, которых служба "Защита идентификации" определила как выполняющих рискованные действия. Третий API, **signIn**, позволяет запрашивать у Microsoft Graph информацию о входах в Azure AD со специфическими свойствами, связанными с состоянием, подробностями и уровнем риска. Эта статья познакомит вас к работе с запросы этих API-интерфейсов и подключение к Microsoft Graph. Дополнительные сведения, полную документацию и доступ к Graph Explorer можно получить на [сайте Microsoft Graph](https://graph.microsoft.io/) или по следующим ссылкам на документацию по API-интерфейсам:

* [API identityRiskEvents](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)
* [API riskyUsers](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [API signIn](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)


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

2. В области навигации слева щелкните **Active Directory**. 
   
    ![Создание приложения](./media/graph-get-started/41.png)


3. В разделе **Управление** щелкните **Свойства**.

    ![Создание приложения](./media/graph-get-started/42.png)

4. Скопируйте имя домена.


## <a name="create-a-new-app-registration"></a>Создание регистрации приложения

1. На странице **Active Directory** в разделе **Управление** щелкните **Регистрация приложений**.

    ![Создание приложения](./media/graph-get-started/42.png)


2. В меню в верхней части страницы щелкните **Регистрация нового приложения**.
   
    ![Создание приложения](./media/graph-get-started/43.png)

3. На странице **Создание** выполните следующие действия.
   
    ![Создание приложения](./media/graph-get-started/44.png)

    a. В текстовом поле **Имя** введите имя приложения (например, приложение API события риска AADIP).
   
    2\. В качестве **типа** выберите **Веб-приложение и/или веб-API**.
   
    c. В текстовом поле **URL-адрес входа** введите `http://localhost`.

    d. Нажмите кнопку **Создать**.

4. Чтобы открыть страницу **Параметры**, в списке приложений щелкните только что созданную регистрацию приложения. 

5. Скопируйте **идентификатор приложения**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Предоставление приложению разрешения на использование API

1. На странице **Параметры** щелкните **Необходимые разрешения**.
   
    ![Создание приложения](./media/graph-get-started/15.png)

2. На странице **Необходимые разрешения** на панели инструментов вверху нажмите кнопку **Добавить**.
   
    ![Создание приложения](./media/graph-get-started/16.png)
   
3. На странице **Добавить доступ через API** щелкните **Выбор API**.
   
    ![Создание приложения](./media/graph-get-started/17.png)

4. На странице **Выбор API** выберите **Microsoft Graph**, а затем нажмите кнопку **Выбор**.
   
    ![Создание приложения](./media/graph-get-started/18.png)

5. На странице **Добавить доступ через API** щелкните **Выбрать разрешения**.
   
    ![Создание приложения](./media/graph-get-started/19.png)

6. На странице **Включение доступа** щелкните **Read all identity risk information** (Прочитать все сведения о рисках идентификаторов), а затем щелкните **Выбор**.
   
    ![Создание приложения](./media/graph-get-started/20.png)

7. На странице **Добавить доступ через API** щелкните **Готово**.
   
    ![Создание приложения](./media/graph-get-started/21.png)

8. На странице **Требуемые разрешения** щелкните **Предоставить разрешения** и **Да**.
   
    ![Создание приложения](./media/graph-get-started/22.png)



## <a name="get-an-access-key"></a>Получение ключа доступа

1. На странице **Параметры** щелкните **Ключи**.
   
    ![Создание приложения](./media/graph-get-started/23.png)

2. На странице **Ключи** выполните следующие действия.
   
    ![Создание приложения](./media/graph-get-started/24.png)

    a. В текстовом поле **Описание ключа** введите описание (например, *Событие риска AADIP*).
    
    2\. Для параметра **Длительность** выберите значение **Через один год**.

    c. Выберите команду **Сохранить**.
   
    d. Скопируйте значение ключа и вставьте его в безопасное место.   
   
   > [!NOTE]
   > Если ключ будет утерян, вам нужно будет вернуться в этот раздел и создать новый ключ. Не предоставляйте этот ключ никому: с его помощью кто угодно может получить доступ к вашим данным.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Проверка подлинности в Microsoft Graph и выполнение запросов с помощью API рисковых событий идентификации

На этом этапе вам понадобятся:

- имя домена клиента.

- идентификатор клиента; 

- ключ. 


Чтобы выполнить проверку подлинности, отправьте запрос POST по адресу `https://login.microsoft.com` со следующими параметрами в тексте:

- grant_type: “**client_credentials**”

-  resource: `https://graph.microsoft.com`

- client_id: \<ваш идентификатор клиента\>;

- client_secret: \<ваш ключ\>.


В случае успешного выполнения этот метод возвращает маркер проверки подлинности.  
Для вызова API создайте заголовок со следующим параметром:

    `Authorization`=”<token_type> <access_token>"


При проверке подлинности тип маркера и маркер доступа можно найти в возвращаемом маркере.

Отправьте этот заголовок как запрос по следующему URL-адресу API: `https://graph.microsoft.com/beta/identityRiskEvents`

В случае успеха в ответ вы получите коллекцию событий риска идентификаторов и связанных данных в формате OData JSON. Эти данные можно проанализировать и обработать по своему усмотрению.

Ниже приведен пример кода для аутентификации и вызова API с помощью PowerShell.  
Просто добавьте свои идентификатор клиента, секретный ключ и домен клиента.

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

## <a name="query-the-apis"></a>Запросы к API-интерфейсам

Эти три API-интерфейса предоставляют множество возможностей для получения информации о пользователях и входах в вашу организацию, связанных с рискованными действиями. Ниже приведены некоторые варианты использования этих API и связанные примеры запросов. Вы можете выполнить эти запросы, используя приведенный выше пример кода или [песочницу Graph](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-the-high-risk-and-medium-risk-events-identityriskevents-api"></a>Получение списка событий риска высокого и среднего уровня (API identityRiskEvents)

События со средним и высоким уровнем риска представляют собой события, которые могут активировать политики защиты идентификации (риска входа в систему или пользователя). Исправление этих событий должно быть приоритетным, так как они имеют среднюю или высокую вероятность того, что пользователь, пытающийся войти в систему, не является законным владельцем удостоверения. 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Получение списка всех пользователей, успешно прошедших многофакторную проверку подлинности, активированную политикой рискованных входов (API riskyUsers)

Чтобы понять влияние основанных на рисках политик Защиты идентификации на вашу организацию, вы можете запросить всех пользователей, успешно прошедших многофакторную проверку подлинности, активированную политикой рискованных входов. Эта информация поможет понять, каких пользователей Защита идентификации может ошибочно определить как рискованных, а также какие из ваших уполномоченных пользователей могут выполнять действия, которые искусственный интеллект считает рискованными.

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
Теперь вы можете запрашивать рисковые события идентификации и использовать данные по своему усмотрению.


Дополнительные сведения о Microsoft Graph и инструкции по созданию приложения с помощью API Graph см. в [документации](https://docs.microsoft.com/graph/overview), а также на [веб-сайте Microsoft Graph](https://developer.microsoft.com/graph). 


Связанные сведения:

-  [Защита идентификации Azure Active Directory.](../active-directory-identityprotection.md)

-  [Типы событий риска, обнаруживаемые защитой идентификации Azure Active Directory](../reports-monitoring/concept-risk-events.md)

- [Microsoft Graph](https://developer.microsoft.com/graph/)

- [Overview of Microsoft Graph (Обзор Microsoft Graph)](https://developer.microsoft.com/graph/docs)

- [Azure AD Identity Protection Service Root (Корень службы защиты идентификации Azure AD)](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)