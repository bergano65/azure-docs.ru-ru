---
title: Одностраничное приложение (вход) — платформа Microsoft Identity
description: Сведения о создании одностраничного приложения (вход)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bf614a5523e78fc72918db973ef8d738a171fff
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031775"
---
# <a name="single-page-application---sign-in"></a>Одностраничное приложение-вход

Узнайте, как добавить вход в код для приложения с одной страницей.

Прежде чем можно будет получить маркеры для доступа к API в приложении, вам потребуется контекст пользователя, прошедшего проверку подлинности. Вход пользователей в приложение в MSAL. js можно выполнять двумя способами:

* [Вход во всплывающем окне](#sign-in-with-a-pop-up-window) с помощью `loginPopup` метода
* [Вход с](#sign-in-with-redirect) помощью `loginRedirect` метода Redirect

При необходимости можно также передать области API, для которых требуется согласие пользователя во время входа.

> [!NOTE]
> Если у приложения уже есть доступ к контексту пользователя, прошедшему проверку подлинности, или маркеру идентификации, можно пропустить шаг входа и получить токены напрямую. Дополнительные сведения см. в разделе [SSO без имени входа msal. js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Выбор между всплывающим окном или перенаправлением

В приложении нельзя использовать сочетание методов всплывающего окна и перенаправления. Выбор между всплывающим окном или перенаправлением зависит от потока приложения.

* Если вы не хотите, чтобы пользователь перешел со страницы основного приложения во время проверки подлинности, рекомендуется использовать всплывающие методы. Так как перенаправление проверки подлинности происходит во всплывающем окне, состояние основного приложения сохраняется.

* В некоторых случаях может потребоваться использовать методы перенаправления. Если у пользователей приложения есть ограничения браузера или политики, в которых отключены всплывающие окна, можно использовать методы перенаправления. Используйте методы перенаправления с браузером Internet Explorer, так как при обработке всплывающих окон обнаружены определенные [проблемы с Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) .

## <a name="sign-in-with-a-pop-up-window"></a>Вход с помощью всплывающего окна

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

Угловая оболочка MSAL позволяет защищать определенные маршруты в приложении, просто добавляя `MsalGuard` к определению маршрута. Это условие вызывает метод для входа при доступе к этому маршруту.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Для работы с всплывающими окнами включите `popUp` параметр конфигурации. Вы также можете передать области, требующие согласия, следующим образом:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Вход с помощью перенаправления

### <a name="javascript"></a>JavaScript

Методы перенаправления не возвращают обещание из-за переходов от основного приложения. Чтобы обработать возвращенные токены и получить к ним доступ, необходимо будет зарегистрировать обратные вызовы успеха и ошибки перед вызовом методов перенаправления.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

Код описан выше, как описано выше в разделе Вход с помощью всплывающего окна. Поток по умолчанию — Redirect.

> [!NOTE]
> Маркер идентификатора не содержит области с ограниченным объемом и представляет пользователя, прошедшего проверку подлинности. Выдаваемые области возвращаются в маркере доступа, который будет получен на следующем шаге.

## <a name="sign-out"></a>Выйти

Библиотека MSAL предоставляет `logout` метод, который очистит кэш в хранилище браузера и отправляет запрос на выход в Azure AD. После выхода по умолчанию он перенаправляется обратно на начальную страницу приложения.

Вы можете настроить универсальный код ресурса (URI), на который будет осуществляться перенаправление после выхода, задав параметр `postLogoutRedirectUri`. Этот URI также должен быть зарегистрирован в качестве URI выхода при регистрации приложения.

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Получение маркера для приложения](scenario-spa-acquire-token.md)
