---
title: Единый вход одностраничного приложения — платформа Microsoft Identity | Службы
description: Узнайте, как создать одностраничное приложение (вход)
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
ms.openlocfilehash: 5d2494a7ad89c9e9b3a525ddd04290a08eb3af58
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962429"
---
# <a name="single-page-application-sign-in"></a>Одностраничное приложение: вход

Узнайте, как добавить вход в код для приложения с одной страницей.

Прежде чем можно будет получить маркеры для доступа к API в приложении, необходим контекст пользователя, прошедшего проверку подлинности. Вход пользователей в приложение в MSAL. js можно выполнять двумя способами:

* [Всплывающее окно](#sign-in-with-a-pop-up-window)с помощью метода `loginPopup`
* [Перенаправление](#sign-in-with-redirect)с помощью метода `loginRedirect`

При необходимости можно также передать области API, для которых требуется согласие пользователя во время входа в систему.

> [!NOTE]
> Если у приложения уже есть доступ к контексту пользователя, прошедшему проверку подлинности, или маркеру идентификации, можно пропустить шаг входа и получить токены напрямую. Дополнительные сведения см. в разделе [SSO без имени входа MSAL. js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Выбор между всплывающим окном или перенаправлением

В приложении нельзя использовать методы всплывающего окна и перенаправления. Выбор между всплывающим окном или перенаправлением зависит от потока приложения:

* Если вы не хотите, чтобы пользователи переходят со страницы основного приложения во время проверки подлинности, рекомендуется использовать метод всплывающего окна. Так как перенаправление проверки подлинности происходит во всплывающем окне, состояние основного приложения сохраняется.

* Если у пользователей есть ограничения браузера или политики, в которых отключены всплывающие окна, можно использовать метод перенаправления. Используйте метод Redirect в браузере Internet Explorer, так как существуют [Известные проблемы с всплывающими окнами в Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

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

Угловая оболочка MSAL позволяет защищать определенные маршруты в приложении путем добавления `MsalGuard` к определению маршрута. Это условие вызывает метод для входа при доступе к этому маршруту.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Для интерфейса всплывающего окна включите параметр конфигурации `popUp`. Вы также можете передать области, требующие согласия, следующим образом:

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

## <a name="sign-in-with-redirect"></a>Вход с перенаправлением

### <a name="javascript"></a>JavaScript

Методы перенаправления не возвращают обещание из-за перемещения из основного приложения. Чтобы обработать возвращенные токены и получить к ним доступ, необходимо зарегистрировать обратные вызовы в случае успеха и ошибки перед вызовом методов перенаправления.

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
> Маркер идентификатора не содержит области с ограниченным объемом и представляет пользователя, прошедшего проверку подлинности. Области с ограниченным доступом возвращаются в маркере доступа, который вы получите на следующем шаге.

## <a name="sign-out"></a>Функция выхода

Библиотека MSAL предоставляет `logout` метод, который очищает кэш в хранилище браузера и отправляет запрос на выход в Azure Active Directory (Azure AD). После выхода библиотека по умолчанию перенаправляется обратно на начальную страницу приложения.

Вы можете настроить универсальный код ресурса (URI), на который будет осуществляться перенаправление после выхода, установив `postLogoutRedirectUri`. Этот URI также должен быть зарегистрирован в качестве URI выхода при регистрации приложения.

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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Получение маркера для приложения](scenario-spa-acquire-token.md)
