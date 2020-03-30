---
title: Передайте пользовательское состояние в запросах на аутентификацию (MSAL.js) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как передать пользовательское значение параметра состояния в запросе на проверку подлинности с помощью библиотеки подлинности Майкрософт для JavaScript (MSAL.js).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084931"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Передайте пользовательское состояние в запросах на аутентификацию с помощью MSAL.js

Параметр *состояния,* определенный OAuth 2.0, включен в запрос на проверку подлинности, а также возвращается в ответ на токен для предотвращения атак подлога запроса на кросс-сайт. По умолчанию библиотека подлинности Microsoft для JavaScript (MSAL.js) передает случайно сгенерированное уникальное значение *параметров состояния* в запросах на аутентификацию.

Параметр состояния также может быть использован для кодирования информации о состоянии приложения перед перенаправлением. Вы можете передать состояние пользователя в приложении, например страницу или представление, на которой он находился, в качестве ввода этого параметра. Библиотека MSAL.js позволяет передавать пользовательское состояние в `Request` качестве параметра состояния в объекте:

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
> Если вы хотите пропустить кэшированный маркер и перейти на `forceRefresh` сервер, пожалуйста, перейдите в boolean в объект authentication Parameters, используемый для входа / маркер запроса.
> `forceRefresh`не должны использоваться по умолчанию из-за влияния на работу приложения.
> Опираясь на кэш даст пользователям лучший опыт.
> Пропуск кэша должен использоваться только в сценариях, когда вы знаете, что кэшированные данные не имеют актуальной информации.
> Например, инструмент Admin, который добавляет роли пользователю, который должен получить новый маркер с обновленными ролями.

Пример:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Пройденое в состоянии придатилось к уникальному GUID, установленному MSAL.js при отправке запроса. Когда ответ возвращается, MSAL.js проверяет соответствие состояния, а затем `Response` возвращает `accountState`пользовательский данный, передаваемый в состоянии объекта, как .

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

Чтобы узнать больше, прочитайте о [создании одностраничного приложения (SPA)](scenario-spa-overview.md) с использованием MSAL.js.