---
title: Инициализация клиентских приложений (Библиотека проверки подлинности Microsoft для JavaScript) | Службы
description: Сведения об инициализации клиентских приложений с помощью библиотеки проверки подлинности Майкрософт для JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d36def4faa98f4b8e42c93cf3e222c2ec7ca89a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073845"
---
# <a name="initialize-client-applications-using-msaljs"></a>Инициализация клиентских приложений с помощью MSAL. js
В этой статье описывается инициализация библиотеки проверки подлинности Майкрософт для JavaScript (MSAL. js) с экземпляром приложения агента пользователя. Приложение агента пользователя — это форма общедоступного клиентского приложения, в котором клиентский код выполняется в агенте пользователя, например в веб-браузере. Эти клиенты не сохраняют секреты, так как контекст браузера открыто доступен. Дополнительные сведения о типах клиентских приложений и параметрах конфигурации приложений см. в [обзоре](msal-client-applications.md).

## <a name="prerequisites"></a>Предварительные требования
Перед инициализацией приложения сначала необходимо [зарегистрировать его в портал Azure](scenario-spa-app-registration.md) , чтобы приложение можно было интегрировать с платформой Microsoft Identity. После регистрации может потребоваться следующая информация (которую можно найти в портал Azure):

- Идентификатор клиента (строка, представляющая идентификатор GUID для приложения)
- URL-адрес поставщика удостоверений (с именем экземпляра) и аудитория входа для приложения. Эти два параметра вместе называются полномочиями.
- Идентификатор клиента, если вы пишете бизнес-приложение исключительно для вашей организации (также называется одноклиентским приложением).
- Для веб-приложений необходимо также задать redirectUri, где поставщик удостоверений вернется в приложение с маркерами безопасности.

## <a name="initializing-applications"></a>Инициализация приложений

Вы можете использовать MSAL. js, как показано в простом приложении JavaScript или typescript. Инициализируйте контекст проверки подлинности `UserAgentApplication` MSAL, создав экземпляр с помощью объекта конфигурации. Минимально необходимый файл конфигурации для инициализации MSAL. js — это идентификатор clientID вашего приложения, который вы должны получить на портале регистрации приложений.

Для методов проверки подлинности с`loginRedirect` потоками перенаправления (и `acquireTokenRedirect`) необходимо явно зарегистрировать обратный вызов для успеха или ошибки `handleRedirectCallback()` с помощью метода. Это необходимо, так как потоки перенаправления не возвращают обещания в качестве методов с всплывающим окном.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
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

MSAL. js предназначен для использования одного экземпляра и конфигурации `UserAgentApplication` для представления одного контекста проверки подлинности. Не рекомендуется использовать несколько экземпляров, так как они вызывают конфликтующие записи кэша и поведение в браузере.

## <a name="configuration-options"></a>Параметры конфигурации

В MSAL. js показан объект конфигурации, который предоставляет возможность группировки настраиваемых параметров, доступных для создания экземпляра `UserAgentApplication`.

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

Ниже приведен общий набор настраиваемых параметров, которые сейчас поддерживаются в объекте конфигурации.

- **clientID**: Обязательный элемент. ClientID вашего приложения, вы должны получить его на портале регистрации приложений.

- **центр сертификации**: Необязательный элемент. URL-адрес, указывающий каталог, из которого MSAL может запрашивать маркеры. Значение по умолчанию: `https://login.microsoftonline.com/common`.
    * В Azure&lt;AD это форма HTTPS://&lt;&gt;/instance, где&lt; `https://login.microsoftonline.com`экземпляр—этодоменпоставщикаудостоверений(например,)и&gt; &gt; аудитория&gt; — это идентификатор, представляющий аудиторию входа. &lt; Это могут быть следующие значения:
        * `https://login.microsoftonline.com/<tenant>`— Клиент — это домен, связанный с клиентом, например contoso.onmicrosoft.com, или идентификатор GUID, представляющий `TenantID` свойство каталога, используемого только для входа пользователей определенной организации.
        * `https://login.microsoftonline.com/common`— Используется для входа пользователей с рабочими или учебными учетными записями или с личной учетной записью Майкрософт.
        * `https://login.microsoftonline.com/organizations/`— Используется для входа пользователей с рабочими и учебными учетными записями.
        * `https://login.microsoftonline.com/consumers/`— Используется для входа пользователей только с личной учетная запись Майкрософт (Live).
    * В Azure AD B2C это форма `https://<instance>/tfp/<tenant>/<policyName>/`, где instance — это Azure AD B2C домен, клиент — имя клиента Azure AD B2C, policyName — имя применяемой политики B2C.


- **валидатеаусорити**: Необязательный элемент.  Проверка издателя токенов. Значение по умолчанию — `true`. Для B2C приложений, так как значение полномочия известно и может различаться для каждой политики, проверка центра не будет работать и должна быть установлена в `false`значение.

- **redirectUri**: Необязательный элемент.  Универсальный код ресурса (URI) перенаправления приложения, на который можно отправлять ответы аутентификации для их получения приложением. Он должен точно соответствовать одному из URI перенаправления, зарегистрированных на портале. По умолчанию — `window.location.href`.

- **постлогаутредиректури**: Необязательный элемент.  Перенаправляет пользователя на `postLogoutRedirectUri` после выхода. Значение по умолчанию — `redirectUri`.

- **навигатетологинрекуестурл**: Необязательный элемент. Возможность отключить навигацию по умолчанию для начальной страницы после входа. Значение по умолчанию — true. Используется только для потоков перенаправления.

- **качелокатион**: Необязательный элемент.  Задает для хранилища браузера значение `localStorage` или `sessionStorage`. Значение по умолчанию — `sessionStorage`.

- **стореаусстатеинкукие**: Необязательный элемент.  Этот флаг появился в MSAL. js v 0.2.2 в качестве исправления для [проблем цикла проверки](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) подлинности в Microsoft Internet Explorer и Microsoft ребр. Чтобы воспользоваться этим `storeAuthStateInCookie` исправлением, установите для флага значение true. Если этот параметр включен, MSAL. js сохранит состояние запроса проверки подлинности, необходимое для проверки последовательностей проверки подлинности в файлах cookie браузера. По умолчанию этот флаг имеет значение `false`.

- **средство ведения журнала**: Необязательный элемент.  Объект средства ведения журнала с экземпляром обратного вызова, который может быть предоставлен разработчиком для использования и публикации журналов особым образом. Дополнительные сведения о передаче объекта средства ведения журнала см. в разделе [ведение журнала с помощью msal. js](msal-logging.md).

- **лоадфраметимеаут**: Необязательный элемент.  Время ожидания (в миллисекундах) перед откликом на продление срока действия маркера из Azure AD. Значение по умолчанию — 6 секунд.

- **токенреневалоффсетсекондс**: Необязательный элемент. Число миллисекунд, которое задает окно смещения, необходимое для продления срока действия маркера. Значение по умолчанию — 300 миллисекунд.

Они применимы только к передаваемым из библиотеки MSAL-оболочек.
- **унпротектедресаурцес**: Необязательный элемент.  Массив URI, которые являются незащищенными ресурсами. MSAL не будет подключать маркер к исходящим запросам, имеющим эти URI. По умолчанию — `null`.

- **протектедресаурцемап**: Необязательный элемент.  Это сопоставление ресурсов с областями, используемыми MSAL для автоматического присоединения маркеров доступа в вызовах веб-API. Для ресурса получен один маркер доступа. Поэтому можно сопоставить путь определенного ресурса следующим образом: {"https://graph.microsoft.com/v1.0/me", ["user.read"]}, или URL-адрес ресурса в виде приложения: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Это необходимо для вызовов CORS. По умолчанию — `null`.
