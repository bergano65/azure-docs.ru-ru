---
title: Передача пользовательского состояния в запросах проверки подлинности (библиотека проверки подлинности Майкрософт для JavaScript) | Azure
description: Узнайте, как передать значение параметра пользовательского состояния в запрос на проверку подлинности с помощью библиотеки проверки подлинности Майкрософт для JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66420503"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Передача пользовательского состояния в запросах проверки подлинности с помощью MSAL.js
*Состояние* параметр, согласно OAuth 2.0, включается в запрос на проверку подлинности и также возвращается в ответе маркера для предотвращения атак с подделкой межсайтовых запросов. По умолчанию библиотека проверки подлинности Майкрософт для JavaScript (MSAL.js) передает случайно сгенерированное уникальное *состояние* значение параметра в запросах проверки подлинности.

Параметр state используется также для кодирования сведений из состояния приложения перед перенаправления. Вы можете передать состояние пользователя в приложении, например странице или представлении, открытой в тот момент, как входные данные для этого параметра. Библиотека MSAL.js позволяет передать пользовательские состояния в качестве параметра состояния в `Request` объекта:

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

Пример:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: “page_url”
}

myMSALObj.loginPopup(loginRequest);
```

Состояние, переданное добавляется уникальный идентификатор GUID, задается MSAL.js при отправке запроса. Если возвращается ответ, MSAL.js проверяет состояние соответствия, а затем возвращает переданный состояния в пользовательский `Response` объекта в виде `accountState`.

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

Чтобы узнать больше, ознакомьтесь с [построение одностраничного приложения (SPA)](scenario-spa-overview.md) с помощью MSAL.js.