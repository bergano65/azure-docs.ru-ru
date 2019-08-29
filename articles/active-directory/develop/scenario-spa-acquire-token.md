---
title: Одностраничное приложение (получение маркера для вызова API) — платформа Microsoft Identity
description: Сведения о создании одностраничного приложения (получение маркера для вызова API)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135711"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Одностраничное приложение — получение маркера для вызова API

Шаблон для получения маркеров для API-интерфейсов с помощью MSAL. js — сначала попытайтесь выполнить запрос маркера `acquireTokenSilent` в автоматическом режиме с помощью метода. При вызове этого метода библиотека сначала проверяет кэш в хранилище браузера, чтобы узнать, существует ли допустимый маркер, и возвращает его. Если в кэше нет допустимого маркера, он отправляет запрос на автоматическую лексему в Azure Active Directory (Azure AD) из скрытого IFRAME. Этот метод также позволяет библиотеке обновлять маркеры. Дополнительные сведения о сеансах единого входа и значениях времени существования маркеров в Azure AD см. в разделе [время существования маркеров](active-directory-configurable-token-lifetimes.md).

Неуспешные запросы маркера к Azure AD могут завершиться сбоем по некоторым причинам, таким как просроченный сеанс Azure AD или изменение пароля. В этом случае можно вызвать один из интерактивных методов (который предложит пользователю) получить маркеры.

* [Получение маркера во всплывающем окне](#acquire-token-with-a-pop-up-window) с помощью`acquireTokenPopup`
* [Получение токена с](#acquire-token-with-redirect) перенаправлением с помощью`acquireTokenRedirect`

**Выбор между всплывающим окном или перенаправлением**

 В приложении нельзя использовать сочетание методов всплывающего окна и перенаправления. Выбор между всплывающим окном или перенаправлением зависит от потока приложения.

* Если вы не хотите, чтобы пользователь перешел со страницы основного приложения во время проверки подлинности, рекомендуется использовать всплывающие методы. Так как перенаправление проверки подлинности происходит во всплывающем окне, состояние основного приложения сохраняется.

* В некоторых случаях может потребоваться использовать методы перенаправления. Если у пользователей приложения есть ограничения браузера или политики, в которых отключены всплывающие окна, можно использовать методы перенаправления. Также рекомендуется использовать методы перенаправления с браузером Internet Explorer, так как при обработке всплывающих окон возникают определенные [проблемы с Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) .

Вы можете задать области API, которые должен включить маркер доступа при создании запроса маркера доступа. Обратите внимание, что все запрошенные области могут быть не предоставлены в маркере доступа и зависят от согласия пользователя.

## <a name="acquire-token-with-a-pop-up-window"></a>Получение маркера во всплывающем окне

### <a name="javascript"></a>JavaScript

Приведенный выше шаблон, использующий методы для всплывающего окна:

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

MSAL угловая оболочка предоставляет возможность добавления перехватчика HTTP, который автоматически получает маркеры доступа и прикрепляет их к HTTP-запросам к API.

Вы можете указать области для API-интерфейсов в `protectedResourceMap` параметре конфигурации, который мсалинтерцептор будет запрашивать при автоматическом получении маркеров.

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

В случае успеха и сбоя при получении автоматического получения маркера MSAL в радиальном режиме предоставляет обратные вызовы, на которые можно подписываться. Также важно отменить подписывание.

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

Кроме того, можно явно получить маркеры с помощью методов получения маркера, как описано в разделе Основная библиотека MSAL. js.

## <a name="acquire-token-with-redirect"></a>Получение маркера с перенаправлением

### <a name="javascript"></a>JavaScript

Шаблон описан выше, но показан с помощью метода Redirect для получения маркера в интерактивном режиме. Вам нужно будет зарегистрировать обратный вызов перенаправления, как упоминалось выше.

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

## <a name="request-for-optional-claims"></a>Запрос дополнительных утверждений
Вы можете запросить дополнительные утверждения в приложении, чтобы указать, какие дополнительные утверждения следует включить в токены для вашего приложения. Чтобы запросить необязательные утверждения в id_token, можно отправить объект утверждений переведенные в поле Клаимсрекуест класса AuthenticationParameters. TS.

Необязательные утверждения можно использовать в следующих целях:

- Для включения дополнительных утверждений в токены для вашего приложения.
- изменить поведение определенных утверждений в токенах, возвращаемых Azure AD;
- добавлять пользовательские утверждения для приложения и обращаться к ним.


### <a name="javascript"></a>JavaScript
```javascript
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```
Дополнительные сведения о необязательных утверждениях см. в статье извлечение [дополнительных утверждений](active-directory-optional-claims.md) .


### <a name="angular"></a>Angular

Это то же самое, что описано выше.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Вызов веб-API](scenario-spa-call-api.md)
