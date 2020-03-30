---
title: Приобретите токен для вызова веб-API (одностраничных приложений) - платформа идентификации Майкрософт Azure
description: Узнайте, как создать одностраничное приложение (приобрести токен для вызова API)
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
ms.openlocfilehash: d5d48a2fc7aca184cf8b6e7761584a8800ca5151
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160072"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Одностраничное приложение: Приобрести токен для вызова API

Шаблон для приобретения токенов для AIS с MSAL.js заключается в `acquireTokenSilent` том, чтобы сначала попытаться запрос на тихий маркер с помощью метода. Когда этот метод вызывается, библиотека сначала проверяет кэш в хранилище браузера, чтобы увидеть, существует ли допустимый маркер, и возвращает его. Когда в кэше нет действительного маркера, он отправляет запрос на немышленный маркер в каталог Azure Active Directory (Azure AD) из скрытого iframe. Этот метод также позволяет библиотеке обновлять токены. Для получения дополнительной информации о едином сеансе всходной [сессии](active-directory-configurable-token-lifetimes.md)и значениях срока службы маркеров в Azure AD см.

Запросы на тихие маркеры Azure AD могут выйти из строя по причинам, например, с истекшим сеансом Azure AD или изменением пароля. В этом случае можно вызвать один из интерактивных методов (который подскажет пользователю) приобрести токены:

* [Всплывающее окно](#acquire-a-token-with-a-pop-up-window), с помощью`acquireTokenPopup`
* [Перенаправить,](#acquire-a-token-with-a-redirect)с помощью`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Выберите между всплывающими или перенаправить опыт

 Вы не можете использовать как всплывающие, так и перенаправить методы в приложении. Выбор между всплывающими или перенаправить опыт зависит от потока приложения:

* Если вы не хотите, чтобы пользователи отошли от вашей главной страницы приложения во время проверки подлинности, мы рекомендовали всплывающий метод. Поскольку перенаправление аутентификации происходит в всплывающем окне, состояние основного приложения сохраняется.

* Если у пользователей есть ограничения браузера или политики, где всплывающие окна отключены, можно использовать метод перенаправления. Используйте метод перенаправления с браузером Internet Explorer, потому что есть [известные проблемы с всплывающими окнами на Internet Explorer.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)

Можно настроить области API, которые необходимо включить в маркер доступа при создании запроса на маркер доступа. Обратите внимание, что все запрошенные области могут быть не предоставлены в маркере доступа. Это зависит от согласия пользователя.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Приобретите токен с всплывающее окно

# <a name="javascript"></a>[Javascript](#tab/javascript)

Следующий код сочетает в себе ранее описанный шаблон с методами всплывающих интерфейсов:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
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

# <a name="angular"></a>[Angular](#tab/angular)

Обертка MSAL Angular предоставляет перехватчик HTTP, который будет автоматически получать токены доступа молча и прикреплять их к запросам HTTP к AA.

Можно указать области для AIS `protectedResourceMap` в опции конфигурации. `MsalInterceptor`будет запрашивать эти области при автоматическом приобретении токенов.

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

Для успеха и неудачи приобретения бесшумного токена, MSAL Angular предоставляет обратные вызовы, на которые вы можете подписаться. Также важно помнить, чтобы отписаться.

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

Кроме того, вы можете явно приобретать токены, используя методы приобретения токенов, описанные в основной библиотеке MSAL.js.

---

## <a name="acquire-a-token-with-a-redirect"></a>Приобретение токена с перенаправлением

# <a name="javascript"></a>[Javascript](#tab/javascript)

Следующий шаблон описан ранее, но показан с помощью метода перенаправления для приобретения токенов в интерактивном режиме. Вам нужно будет зарегистрировать перенаправление обратного вызова, как упоминалось ранее.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Запрос дополнительных претензий

Дополнительные требования можно использовать для следующих целей:

- Включите дополнительные требования в токены для приложения.
- изменить поведение определенных утверждений в токенах, возвращаемых Azure AD;
- добавлять пользовательские утверждения для приложения и обращаться к ним. 

Чтобы запросить `IdToken`дополнительные претензии в, вы `claimsRequest` можете отправить `AuthenticationParameters.ts` строку претензий объекта в поле класса.

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

Чтобы узнать больше, [см.](active-directory-optional-claims.md)

# <a name="angular"></a>[Angular](#tab/angular)

Этот код такой же, как описано ранее.

---

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Вызов веб-API](scenario-spa-call-api.md)
