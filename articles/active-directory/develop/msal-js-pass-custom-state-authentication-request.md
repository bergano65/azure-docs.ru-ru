---
title: Передача пользовательского состояния в запросах проверки подлинности (MSAL. js) | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как передать значение параметра настраиваемого состояния в запросе на проверку подлинности с помощью библиотеки проверки подлинности Майкрософт для JavaScript (MSAL. js).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4cb0f3d054f9afd0c606f80fd6fc5d553eff806
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916321"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Передача пользовательского состояния в запросах проверки подлинности с помощью MSAL. js
Параметр *State* , как определено в OAuth 2,0, включается в запрос проверки подлинности и возвращается в ответе маркера, чтобы предотвратить атаки подделки межсайтовых запросов. По умолчанию библиотека проверки подлинности Майкрософт для JavaScript (MSAL. js) передает в запросах проверки подлинности случайным образом уникальное значение параметра *состояния* .

Параметр State также можно использовать для кодирования сведений о состоянии приложения перед перенаправлением. Можно передать состояние пользователя в приложении, например страницу или представление, в котором они были включены, в качестве входных данных для этого параметра. Библиотека MSAL. js позволяет передавать пользовательское состояние в качестве параметра состояния в объект `Request`:

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
};
```

Пример.

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Переданное состояние добавляется к уникальному идентификатору GUID, заданному MSAL. js при отправке запроса. При возвращении ответа MSAL. js проверяет совпадение состояния и возвращает переданное пользователем состояние в объекте `Response` как `accountState`.

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