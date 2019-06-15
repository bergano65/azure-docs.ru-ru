---
title: Одностраничное приложение (получить токен для вызова API) — платформе Microsoft identity
description: Узнайте, как создать одностраничное приложение (получить маркер для вызова API)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138814"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Одностраничное приложение - получить токен для вызова API

Шаблон для приобретения токенов для API-интерфейсы с MSAL.js — сначала попытайтесь автоматический запрос маркера с помощью `acquireTokenSilent` метод. При вызове этого метода, библиотеке сначала проверяет кэш в хранилище браузера, чтобы увидеть, существует ли действительный токен и возвращает его. Если в кэше отсутствует действительный токен, он отправляет автоматический запрос маркера Azure Active Directory (Azure AD) в скрытом запросе iframe. Этот метод также позволяет библиотеке с целью обновления маркеров. Дополнительные сведения о сеанса единого входа и значения времени жизни маркеров в Azure AD, см. в разделе [маркер времени жизни](active-directory-configurable-token-lifetimes.md).

Автоматическая запросов маркеров в Azure AD может завершиться ошибкой по некоторым причинам, например истекшим сроком действия Azure AD сеанса или изменение пароля. В этом случае можно вызвать один из интерактивных методов (которые пользователю будет предложено) для получения маркеров.

* [Получение маркера с всплывающее окно](#acquire-token-with-a-pop-up-window) с помощью `acquireTokenPopup`
* [Получение маркера с помощью перенаправления](#acquire-token-with-redirect) с помощью `acquireTokenRedirect`

**Выбор между всплывающее окно или перенаправления качества**

 Используя сочетание этих методов перенаправления и всплывающее окно нельзя использовать в приложении. Выбор между всплывающее окно или перенаправления интерфейс зависит от хода работы приложения.

* Если вы не хотите покинуть страницу основного приложения во время проверки подлинности пользователю, мы рекомендуем использовать всплывающее методы. Поскольку перенаправления проверки подлинности происходит во всплывающем окне, сохраняется состояние основного приложения.

* Существует несколько случаев, где необходимо использовать методы перенаправления. Если пользователи приложения браузера ограничений или политик где отключены всплывающие окна windows, можно использовать методы перенаправления. Также рекомендуется использовать методы перенаправления с помощью обозревателя Internet Explorer, так как имеются определенные [известные проблемы с Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) при обработке всплывающие окна.

Можно задать области API, которые требуется маркер доступа следует включать при создании запроса маркера доступа. Обратите внимание на то, что все запрошенной области не может быть предоставлено в маркере доступа и зависит от согласия пользователя.

## <a name="acquire-token-with-a-pop-up-window"></a>Получение маркера с помощью всплывающего окна

### <a name="javascript"></a>JavaScript

Выше шаблон, с помощью методов, всплывающих работать:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

### <a name="angular"></a>Angular

MSAL Angular оболочки обеспечивает удобство использования Добавление перехватчик HTTP `MsalInterceptor` которого будет автоматически получать маркеры доступа без вмешательства пользователя и подключить их к HTTP-запросов к API.

Вы можете указать области для API-интерфейсов в `protectedResourceMap` параметр конфигурации, который будет запрашивать MsalInterceptor при автоматически приобретения токенов.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

Успех и отказ автоматической приобретения токена MSAL Angular предоставляет обратные вызовы, которые можно подписаться. Также важно помнить, что отменить подписку.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

В качестве альтернативы Вы можете явным образом получить маркеры, используя методы получения маркеров, как описано в основной библиотеки MSAL.js.

## <a name="acquire-token-with-redirect"></a>Получение маркера с помощью перенаправления

### <a name="javascript"></a>JavaScript

Шаблон, как описано выше, но показано с помощью метода перенаправления, чтобы получить маркер в интерактивном режиме. Обратите внимание, что необходимо зарегистрировать обратный вызов перенаправления, как было сказано выше.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

### <a name="angular"></a>Angular

Это так же, как описано выше.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Вызов веб-API](scenario-spa-call-api.md)
