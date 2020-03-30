---
title: Одноместный входной знак (MSAL.js) Azure
titleSuffix: Microsoft identity platform
description: Узнайте о создании единого взаимодействия с использованием библиотеки подлинности Майкрософт для JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262858"
---
# <a name="single-sign-on-with-msaljs"></a>Единый вход с использованием MSAL.js

Single Sign-On (SSO) позволяет пользователям вводить свои учетные данные один раз, чтобы войти в систему и установить сеанс, который может быть повторно использован в нескольких приложениях без необходимости повторной проверки подлинности. Это обеспечивает бесперебойную работоспособность пользователя и уменьшает повторяющиеся запросы для учетных данных.

Azure AD предоставляет sSO возможности для приложений, установив файл cookie сеанса при первой проверке подлинности пользователя. Библиотека MSAL.js позволяет приложениям использовать это несколькими способами.

## <a name="sso-between-browser-tabs"></a>SSO между вкладками браузера

Когда ваше приложение открыто в нескольких вкладок, и вы первый знак в пользователя на одной вкладке, пользователь также вписываются на другие вкладки без побуждений. MSAL.js кэширует токен ID для `localStorage` пользователя в браузере и подпишет пользователя в приложение на других открытых вкладках.

По умолчанию msAL.js использует, `sessionStorage` что не позволяет обмениваться сеансом между вкладками. Чтобы получить SSO между вкладками, `cacheLocation` убедитесь, что установить в MSAL.js, `localStorage` как показано ниже.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>SSO между приложениями

Когда пользователь проверяет подлинность, cookie-файлы сеанса устанавливаются на домен Azure AD в браузере. MSAL.js полагается на файл cookie сеанса, чтобы предоставить SSO для пользователя между различными приложениями. MSAL.js также кэширует маркеры ID и маркеры доступа пользователя в хранилище браузера на один домен приложения. В результате поведение SSO варьируется в разных случаях:  

### <a name="applications-on-the-same-domain"></a>Приложения на одном домене

Когда приложения размещаются на одном домене, пользователь может войти в приложение один раз, а затем получить аутентификации к другим приложениям без запроса. MSAL.js использует токены, кэшированные для пользователя в домене, для предоставления SSO.

### <a name="applications-on-different-domain"></a>Приложения на разных доменах

Когда приложения размещаются на разных доменах, токены, кэшированные на домене А, не могут быть доступны MSAL.js в домене B.

Это означает, что при входе пользователей в домен A переходит в приложение на домене B, они будут перенаправлены или предложены со страницей Azure AD. Поскольку в Azure AD по-прежнему есть файл cookie-сеанс пользователя, он войдет в систему пользователя, и им не придется повторно вводить учетные данные. Если пользователь имеет несколько учетных записей пользователей в сеансе с Azure AD, пользователю будет предложено выбрать соответствующую учетную запись для входной связи.

### <a name="automatically-select-account-on-azure-ad"></a>Автоматически выберите учетную запись в Azure AD

В некоторых случаях приложение имеет доступ к контексту проверки подлинности пользователя и хочет избежать запроса выбора учетной записи Azure AD при входе в несколько учетных записей.  Это можно сделать несколькими способами:

**Использование идентификатора сеанса (SID)**

Идентификатор сеанса — это [необязательная претензия,](active-directory-optional-claims.md) которая может быть настроена в маркерах идентификаторов. Эта претензия позволяет приложению идентифицировать сеанс Azure AD пользователя независимо от имени учетной записи или имени пользователя. Вы можете передать SID в параметрах `acquireTokenSilent` запроса на вызов. Это позволит Azure AD обойти выбор учетной записи. SID привязан к cookie-файлам сеанса и не будет пересекать контексты браузера.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID может использоваться только с помощью запросов на молчаливую аутентификацию, сделанных по `acquireTokenSilent` вызову в MSAL.js.
Вы можете найти шаги для настройки дополнительных претензий в вашем приложении манифест [здесь](active-directory-optional-claims.md).

**Использование Подсказки входа**

Если у вас нет требования SID настроены или необходимо обойти запрос выбора учетной записи `login_hint` в интерактивных вызовов аутентификации, вы можете сделать это, предоставив в параметрах запроса `domain_hint` и дополнительно, как `extraQueryParameters` в MSAL.js интерактивных методов (`loginPopup`, `loginRedirect` `acquireTokenPopup` и `acquireTokenRedirect`). Пример:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Значения для login_hint и domain_hint можно получить, прочитав претензии, возвращенные в маркер ID для пользователя.

* **LoginHint** должен быть `preferred_username` установлен на претензии в маркере ID.

* **domain_hint** требуется только при использовании /общей власти. Подсказка домена определяется идентификатором арендатора (tid).  Если `tid` претензия в токене ID является `9188040d-6c67-4c5b-b112-36a304b66dad` потребителей. В противном случае, это организации.

Читайте [здесь](v2-oauth2-implicit-grant-flow.md) для получения дополнительной информации о значениях для входа подсказка и домен подсказка.

> [!Note]
> Вы не можете пройти SID и login_hint в то же время. Это приведет к ответу на ошибку.

## <a name="sso-without-msaljs-login"></a>SSO без входа MSAL.js

По замыслу MSAL.js требует, чтобы метод входа был вызван для создания пользовательского контекста, прежде чем получать токены для AIS. Поскольку методы входа являются интерактивными, пользователь видит запрос.

Есть определенные случаи, когда приложения имеют доступ к контексту подлинного пользователя или токену ID через аутентификацию, инициированную в другом приложении, и хотят использовать SSO для приобретения токенов без предварительного входного доступа через MSAL.js.

Примером этого является: пользователь входит в родительское веб-приложение, в котором размещается другое приложение JavaScript, работая в качестве дополнения или плагина.

Опыт SSO в этом сценарии может быть достигнут следующим образом:

Передайте, `sid` если `login_hint` доступно (или `domain_hint`дополнительно) в качестве параметров `acquireTokenSilent` запроса на msAL.js вызова следующим образом:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO в ADAL.js к обновлению MSAL.js

MSAL.js обеспечивает паритет функций с ADAL.js для сценариев аутентификации Azure AD. Чтобы сделать миграцию из ADAL.js в MSAL.js легкой и избежать побуждения пользователей войти снова, библиотека читает токен ID, представляющий сеанс пользователя в кэше ADAL.js, и плавно подписывает пользователя в MSAL.js.  

Чтобы воспользоваться поведением одного ввоза (SSO) при обновлении от ADAL.js, `localStorage` необходимо убедиться, что библиотеки используют сяпинг токены. Установите `cacheLocation` `localStorage` конфигурацию MSAL.js и ADAL.js при инициализации следующим образом:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Как только это настроено, MSAL.js сможет прочитать кэшированное состояние проверенного пользователя в ADAL.js и использовать его для предоставления SSO в MSAL.js.

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о [едином сеансе вспили и](active-directory-configurable-token-lifetimes.md) значениях продолжительности жизни маркеров в Azure AD.
