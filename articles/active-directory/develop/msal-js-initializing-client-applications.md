---
title: Инициализация клиентских приложений MSAL.js Azure
titleSuffix: Microsoft identity platform
description: Узнайте о инициализации клиентских приложений, используя библиотеку подлинности Майкрософт для JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a0a2c5fc971c3f1f3283d95c5617bdf1e88a6a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084041"
---
# <a name="initialize-client-applications-using-msaljs"></a>Инициализация клиентских приложений с использованием MSAL.js
В этой статье описывается инициализация библиотеки подлинности Microsoft для JavaScript (MSAL.js) экземпляром приложения пользователя-агента. Приложение-агент пользователя является формой публичного клиентского приложения, в котором клиентский код выполняется в пользовательском агенте, например в веб-браузере. Эти клиенты не хранят секреты, так как контекст браузера открыт. Чтобы узнать больше о типах клиентских приложений и вариантах конфигурации приложений, прочитайте [обзор.](msal-client-applications.md)

## <a name="prerequisites"></a>Предварительные требования
Прежде чем инициализировать приложение, сначала необходимо [зарегистрировать его на портале Azure,](scenario-spa-app-registration.md) чтобы ваше приложение было интегрировано с платформой идентификации Майкрософт. После регистрации вам может понадобиться следующая информация (которая может быть найдена на портале Azure):

- Идентификатор клиента (строка, представляющая GUID для вашего приложения)
- URL-адрес поставщика идентификационных данных (названный экземпляр) и аудитория для вашего приложения. Эти два параметра коллективно известны как власть.
- Идентификатор клиента, если вы пишете приложение line-of-business исключительно для вашей организации (также названное приложением с одним арендатором).
- Для веб-приложений вам также придется установить redirectUri, где поставщик идентификационных данных вернется в ваше приложение с маркерами безопасности.

## <a name="initializing-applications"></a>Инициализация приложений

Вы можете использовать MSAL.js следующим образом в простом приложении JavaScript/Typescript. Инициализовать контекст проверки `UserAgentApplication` подлинности MSAL путем мгновенного воспроизведения с объектом конфигурации. Минимальная необходимая конфигурация для инициализации MSAL.js является клиентом вашего приложения, который вы должны получить с портала регистрации приложений.

Для методов проверки подлинности`loginRedirect` `acquireTokenRedirect`с перенаправлением потоков (и), вам нужно `handleRedirectCallback()` будет явно зарегистрировать обратный вызов для успеха или ошибки с помощью метода. Это необходимо, поскольку перенаправление потоков не возвращают обещаний, как это делают методы с всплывающими опытами.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL.js предназначен для единого экземпляра `UserAgentApplication` и конфигурации для представления единого контекста аутентификации. Несколько экземпляров не рекомендуется, поскольку они вызывают противоречивые записи кэша и поведение в браузере.

## <a name="configuration-options"></a>Варианты настройки

MSAL.js имеет объект конфигурации, показанный ниже, который обеспечивает группировку `UserAgentApplication`настраиваемых вариантов, доступных для создания экземпляра .

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
    navigateFrameWait?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Ниже приведен общий набор настраиваемых опций, которые поддерживаются в настоящее время в объекте конфигурации:

- **clientID**: Требуется. Клиент вашей заявки, вы должны получить это с портала регистрации приложения.

- **авторитет**: Необязательно. URL-адрес с указанием каталога, от которого MSAL может запрашивать токены. Значение по умолчанию: `https://login.microsoftonline.com/common`.
    * В Azure AD это форма&lt;https://&gt;/&lt;&gt;аудитории &lt;&gt; экземпляра, где экземпляр является `https://login.microsoftonline.com`доменом поставщика идентификационных данных (например, ) и &lt;аудитория&gt; является идентификатором, представляющим аудиторию, представляющую аудиторию. Это могут быть следующие значения:
        * `https://login.microsoftonline.com/<tenant>`- арендатор — это домен, связанный с арендатором, `TenantID` например, contoso.onmicrosoft.com или GUID, представляющий свойство каталога, используемого только для регистрации в пользователях определенной организации.
        * `https://login.microsoftonline.com/common`- Используется для вху-на пользователей с рабочими и школьными учетными записями или личным аккаунтом Майкрософт.
        * `https://login.microsoftonline.com/organizations/`- Используется для вху:нахи пользователей с рабочими и школьными учетными записями.
        * `https://login.microsoftonline.com/consumers/`- Используется для вху-на ввоза пользователей только с личной учетной записью Майкрософт (live).
    * В Azure AD B2C, это `https://<instance>/tfp/<tenant>/<policyName>/`форма , где экземпляр оммазанием является домен Azure AD B2C, т.е. «ваш арендатор-имя».b2clogin.com, арендатор — это имя арендатора Azure AD B2C, т.е. «ваш-арендатор-имя».


- **validateAuthority**: Необязательно.  Проверка эмитента токенов. Значение по умолчанию — `true`. Для приложений B2C, так как значение полномочий известно и может быть различным в отношении политики, проверка полномочий не будет работать и должна быть установлена на `false`.

- **перенаправлениеUri**: Необязательно.  Универсальный код ресурса (URI) перенаправления приложения, на который можно отправлять ответы аутентификации для их получения приложением. Он должен точно соответствовать одному из перенаправления URIs, зарегистрированных на портале. По умолчанию равен `window.location.href`.

- **postLogoutRedirecturi**: Необязательно.  Перенаправляет пользователя на `postLogoutRedirectUri` запрос после выхода. По умолчанию `redirectUri`.

- **navigateToLoginRequestUrl**: Необязательно. Возможность отключить навигацию по умолчанию для запуска страницы после входа. Значение по умолчанию — true. Это используется только для перенаправления потоков.

- **кэшлокация**: Необязательно.  Устанавливает хранилище браузера либо `localStorage` или `sessionStorage`. Значение по умолчанию — `sessionStorage`.

- **storeAuthStateInCookie**: Необязательно.  Этот флаг был введен в MSAL.js v0.2.2 как исправление [проблем с циклом проверки подлинности](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) в Microsoft Internet Explorer и Microsoft Edge. Включите `storeAuthStateInCookie` флаг в верную, чтобы воспользоваться этим исправлением. Когда это включено, MSAL.js будет хранить состояние auth запроса, необходимое для проверки потоков auth в файлах cookie браузера. По умолчанию этот `false`флаг установлен на .

- **регистратор**: Необязательно.  Объект Logger с экземпляром обратного вызова, который может быть предоставлен разработчиком для потребления и публикации журналов на заказ. Для получения подробной информации [logging with msal.js](msal-logging.md)о проходящем объекте регистратора см.

- **loadFrameTimeout**: Необязательно.  Количество миллисекунд бездействия перед ответом на обновление маркеров от Azure AD следует считать приуроченным. По умолчанию 6 секунд.

- **токенRenewalOffsetSeconds**: Необязательно. Количество миллисекунд, которое устанавливает окно смещения, необходимое для обновления токена до истечения срока действия. По умолчанию 300 миллисекунд.

- **navigateFrameWait**: Необязательно. Количество миллисекунд, которое устанавливает время ожидания, прежде чем скрытые iframes перемещаются к месту назначения. По умолчанию 500 миллисекунд.

Они применимы только для того, чтобы передаваться из библиотеки MSAL Angular:
- **незащищенныеРесурсы**: Необязательно.  Массив URIs, которые являются незащищенными ресурсами. MSAL не будет прикреплять токен к исходящие запросам, которые имеют эти URI. По умолчанию равен `null`.

- **защищенныйResourceMap**: Необязательно.  Это отображение ресурсов в области, используемые MSAL для автоматического присоединения токенов доступа в веб-вызовах API. Для ресурса получен один токен доступа. Таким образом, вы можете сопоставить определенный путь ресурса следующим образом:https://graph.microsoft.com/v1.0/me«,», «user.read»»https://graph.microsoft.com/или URL приложения ресурса как: «,» «user.read»,, «mail.send». Это необходимо для вызовов CORS. По умолчанию равен `null`.
