---
title: Передача пользовательского состояния в запросах проверки подлинности (MSAL. js) | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как передать значение параметра настраиваемого состояния в запросе на проверку подлинности с помощью библиотеки проверки подлинности Майкрософт для JavaScript (MSAL. js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77084931"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Передача пользовательского состояния в запросах проверки подлинности с помощью MSAL. js

Параметр *State* , как определено в OAuth 2,0, включается в запрос проверки подлинности и возвращается в ответе маркера, чтобы предотвратить атаки подделки межсайтовых запросов. По умолчанию библиотека проверки подлинности Майкрософт для JavaScript (MSAL. js) передает в запросах проверки подлинности случайным образом уникальное значение параметра *состояния* .

Параметр State также можно использовать для кодирования сведений о состоянии приложения перед перенаправлением. Можно передать состояние пользователя в приложении, например страницу или представление, в котором они были включены, в качестве входных данных для этого параметра. Библиотека MSAL. js позволяет передать пользовательское состояние в качестве параметра состояния в `Request` объекте:

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> Если вы хотите пропустить кэшированный токен и перейти на сервер, передайте логическое значение `forceRefresh` в объект AuthenticationParameters, используемый для создания запроса на вход или маркер.
> `forceRefresh`не следует использовать по умолчанию из-за влияния на производительность приложения.
> Полагаться на кэш, вы предоставите пользователям более удобный интерфейс.
> Пропуск кэша следует использовать только в тех случаях, когда известно, что в данный момент кэшированные данные не содержат актуальных сведений.
> Например средство администрирования, добавляющее к пользователю роли, которым необходимо получить новый маркер с обновленными ролями.

Пример:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Переданное состояние добавляется к уникальному идентификатору GUID, заданному MSAL. js при отправке запроса. При возвращении ответа MSAL. js проверяет совпадение состояния и возвращает переданное пользователем состояние в `Response` объекте в виде. `accountState`

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

Дополнительные сведения см. в статье [Создание одностраничного приложения (SPA)](scenario-spa-overview.md) с помощью MSAL. js.