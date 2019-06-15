---
title: Одностраничное приложение (вход в систему) — платформе Microsoft identity
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
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138804"
---
# <a name="single-page-application---sign-in"></a>Одностраничное приложение - входа

Узнайте, как добавление входа в код для одностраничного приложения.

Прежде чем можно получить маркеры для доступа к API в приложении, вам потребуется контексте прошедшего проверку подлинности пользователя. Пользователей можно войти в приложение MSAL.js двумя способами:

* [Войдите с помощью всплывающего окна](#sign-in-with-a-pop-up-window) с помощью `loginPopup` метод
* [Войдите с помощью перенаправления](#sign-in-with-redirect) с помощью `loginRedirect` метод

При необходимости также можно передать областей из API-интерфейсов, для которого требуется во время входа пользователя.

> [!NOTE]
> Если приложение уже имеет доступ к контекст прошедшим проверку подлинности пользователя или идентификатор токена, вы можете пропустить этап входа и напрямую получать маркеры. Дополнительные сведения см. в разделе [единого входа без имени входа msal.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Выбор между всплывающее окно или перенаправления качества

Используя сочетание этих методов перенаправления и всплывающее окно нельзя использовать в приложении. Выбор между всплывающее окно или перенаправления интерфейс зависит от хода работы приложения.

* Если вы не хотите покинуть страницу основного приложения во время проверки подлинности пользователю, мы рекомендуем использовать всплывающее методы. Поскольку перенаправления проверки подлинности происходит во всплывающем окне, сохраняется состояние основного приложения.

* Существует несколько случаев, где необходимо использовать методы перенаправления. Если пользователи приложения браузера ограничений или политик где отключены всплывающие окна windows, можно использовать методы перенаправления. Использование методов перенаправления с обозревателя Internet Explorer, так как имеются определенные [известные проблемы с Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) при обработке всплывающие окна.

## <a name="sign-in-with-a-pop-up-window"></a>Войдите с помощью всплывающего окна

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
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

MSAL Angular оболочки позволяет защищать определенных маршрутов в приложении, просто добавив `MsalGuard` для определения маршрута. Это условие будет вызывать метод для входа при доступе к этого маршрута.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Для работы всплывающее окно, включите `popUp` параметр конфигурации. Вы также можете передать области, требующие согласия следующим образом:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Войдите с помощью перенаправления

### <a name="javascript"></a>JavaScript

Методы перенаправления не возвращают объект promise, из-за перехода от основного приложения. Для обработки и возвращаемый маркеры доступа, необходимо будет регистрация обратных вызовов успеха и ошибки перед вызовом методов перенаправления.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

Приведенный здесь код является таким же, как описано выше в разделе входа с помощью раздела всплывающее окно. Поток по умолчанию является перенаправление.

> [!NOTE]
> Маркера Идентификации не содержит областей согласие и только представляет пользователя, прошедшего проверку подлинности. Согласие областей возвращаются в маркере доступа, который позволяет запросить на следующем шаге.

## <a name="sign-out"></a>Выход

Предоставляет библиотеку MSAL `logout` метод, который будет очистки кэша в хранилище браузера и отправляет запроса на выход в Azure AD. После выхода он перенаправляет начальная страница приложения по умолчанию.

Вы можете настроить URI, к которому он должен перенаправить после входа out, задав `postLogoutRedirectUri`. Этот URI также должен быть зарегистрирован как URI выхода в регистрации приложения.

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
> [Получение токена для приложения](scenario-spa-acquire-token.md)
