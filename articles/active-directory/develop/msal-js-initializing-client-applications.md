---
title: Инициализировать клиентские приложения (библиотека проверки подлинности Майкрософт для JavaScript) | Azure
description: Дополнительные сведения об инициализации клиентских приложений, использующих библиотеку проверки подлинности Майкрософт для JavaScript (MSAL.js).
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
ms.date: 04/12/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd26f36356affbc8c272bd093757a8482773baf2
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544026"
---
# <a name="initialize-client-applications-using-msaljs"></a>Инициализировать клиентских приложений, использующих MSAL.js
Здесь описывается инициализации библиотеки проверки подлинности Майкрософт для JavaScript (MSAL.js) с экземпляром приложения агента пользователя. Агент пользователя приложения является разновидностью общедоступного клиентского приложения, в котором код клиента выполняется в агенте пользователя такие как веб-браузер. Эти клиенты не храните секретные данные, так как контекст браузера открыто доступен. Дополнительные сведения о типах клиентского приложения и параметры настройки приложения, [Обзор](msal-client-applications.md).

## <a name="prerequisites"></a>Технические условия
Перед инициализацией приложения, необходимо сначала [зарегистрировать его на портале Azure](scenario-spa-app-registration.md) таким образом, чтобы приложение можно интегрировать с платформой Microsoft identity. После регистрации может потребоваться следующие сведения (которые можно найти на портале Azure):

- Идентификатор клиента (строка, представляющая идентификатор GUID для приложения)
- URL-адрес поставщика удостоверений (с именем экземпляра) и аудитории входа для приложения. Эти два параметра, называются полномочия.
- Идентификатор клиента, если вы создаете бизнес приложения исключительно для вашей организации (также именованного приложения одним клиентом).
- Для веб-приложений необходимо также задать redirectUri, куда будет возвращать поставщика удостоверений в приложение с помощью маркеров безопасности.

## <a name="initializing-applications"></a>Инициализация приложения

MSAL.js можно использовать следующим образом в приложении обычный JavaScript или Typescript. Инициализировать контекст проверки подлинности MSAL путем создания экземпляра `UserAgentApplication` с объектом конфигурации. Минимальной конфигурации, необходимые для инициализации MSAL.js это идентификатор clientID приложения, который следует получить на портале регистрации приложения.

Для методов проверки подлинности, перенаправлять потоки (`loginRedirect` и `acquireTokenRedirect`), необходимо явно зарегистрировать обратный вызов для успеха или ошибки через `handleRedirectCallback()` метод. Это необходимо, поскольку потоки перенаправления не возвращают Обещания как методы всплывающее опыт.

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

MSAL.js предназначен для одного экземпляра и параметра `UserAgentApplication` для представления контекста проверки подлинности. Несколько экземпляров не рекомендуется, поскольку они могут вызывать конфликтующих записей кэша и поведение в браузере.

## <a name="configuration-options"></a>Параметры конфигурации

MSAL.js имеет конфигурацию объекта, показано ниже, группируются в настраиваемых параметрах, доступных для создания экземпляра `UserAgentApplication`.

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

Ниже приведен общий набор настраиваемых параметров, которые поддерживаются в настоящее время в объекте конфигурации.

- **clientID**: Обязательный. ClientID приложения, вы должны получить это на портале регистрации приложений.

- **Центр**: Необязательный элемент. URL-адрес, указывающее, MSAL может запрашивать маркеры из каталога. Значение по умолчанию: `https://login.microsoftonline.com/common`.
    * В Azure AD, он имеет форму https://&lt;экземпляр&gt;/&lt;аудитории&gt;, где &lt;экземпляр&gt; — это домен, поставщик удостоверений (например, `https://login.microsoftonline.com`) и &lt;аудитории&gt; — это идентификатор, представляющий аудитории входа в систему. Это может быть следующих значений:
        * `https://login.microsoftonline.com/<tenant>`-клиента — это домен, связанную с клиентом, например contoso.onmicrosoft.com или GUID, отражающий `TenantID` свойство каталога, используемого только для входа пользователей из определенной организации.
        * `https://login.microsoftonline.com/common`-Используется для входа в систему с помощью рабочих и учебных учетных записей или личной учетной записи Майкрософт.
        * `https://login.microsoftonline.com/organizations/`-Используется для входа в систему с помощью рабочих и учебных учетных записей.
        * `https://login.microsoftonline.com/consumers/` -Используется для входа в систему с помощью только личную учетную запись Майкрософт (live).
    * В Azure AD B2C, он имеет вид `https://<instance>/tfp/<tenant>/<policyName>/`, где экземпляр — это домен Azure AD B2C, клиент — это имя клиента Azure AD B2C, policyName — это имя для применения политики B2C.


- **validateAuthority**: Необязательный элемент.  Проверьте издателя маркеров. Значение по умолчанию — `true`. Для приложения B2C, так как значение центра известен и может быть различным в каждой политики, не будет работать центр проверки и должны быть заданы `false`.

- **redirectUri**: Необязательный элемент.  Универсальный код ресурса (URI) перенаправления приложения, на который можно отправлять ответы аутентификации для их получения приложением. Он должен в точности соответствовать одному из универсальных кодов ресурсов (URI) перенаправления, зарегистрированных на портале, но иметь форму URL-адреса. По умолчанию — `window.location.href`.

- **postLogoutRedirectUri**: Необязательный элемент.  Перенаправляет пользователя для `postLogoutRedirectUri` после выхода. Значение по умолчанию — `redirectUri`.

- **navigateToLoginRequestUrl**: Необязательный элемент. Возможность отключения навигации по умолчанию для запуска страницы после входа в систему. Значение по умолчанию — true. Используется только для перенаправления потоков.

- **cacheLocation**: Необязательный элемент.  Задает хранилище браузера либо `localStorage` или `sessionStorage`. Значение по умолчанию — `sessionStorage`.

- **storeAuthStateInCookie**: Необязательный элемент.  Этот флаг появился в MSAL.js v0.2.2 как исправление [проблемы цикл проверки подлинности](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) на Microsoft Internet Explorer и Microsoft Edge. Включить флаг `storeAuthStateInCookie` значение true, чтобы воспользоваться преимуществами это исправить. Когда эта функция включена, MSAL.js сохраняет состояние запроса проверки подлинности, необходимые для проверки потоков проверки подлинности в файлах cookie браузера. По умолчанию этот флаг установлен `false`.

- **Средство ведения журнала**: Необязательный элемент.  Объект средства ведения журнала с экземпляром обратного вызова, которая может быть предоставлена разработчиком, преобразует и не публикует журналы специальным образом. Дополнительные сведения о передаче объект средства ведения журнала, см. в разделе [ведение журналов с помощью msal.js](msal-logging.md).

- **loadFrameTimeout**: Необязательный элемент.  Истекло время ожидания бездействия ответа токена обновления от Azure AD можно рассматривать время в миллисекундах. Значение по умолчанию — 6 секунд.

- **tokenRenewalOffsetSeconds**: Необязательный элемент. Количество миллисекунд в задает окно смещения, необходимые для обновления до истечения срока действия маркера. Значение по умолчанию — 300 миллисекунд.

Они применимы только к передаваться от библиотеку-упаковщик MSAL Angular:
- **unprotectedResources**: Необязательный элемент.  Массив URI, которые являются незащищенных ресурсах. MSAL не присоединит маркер к исходящим запросам, которые имеют эти URI. По умолчанию — `null`.

- **protectedResourceMap**: Необязательный элемент.  Это является сопоставление ресурсов для областей, используемых MSAL для автоматическое присоединение маркеров доступа в вызовы веб-API. Единый маркер доступа получается для ресурса. Поэтому можно сопоставить путь определенного ресурса следующим образом: {» https://graph.microsoft.com/v1.0/me«, [«user.read»]}, или URL-адрес ресурса в виде приложения: {» https://graph.microsoft.com/«, [«user.read», «mail.send»]}. Это необходимо для вызовов CORS. По умолчанию — `null`.
