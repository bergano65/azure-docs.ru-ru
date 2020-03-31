---
title: Одностраничное приложение, восподписанное &- идентификационная платформа Майкрософт Azure
description: Узнайте, как создать одностраничное приложение (ввесть)
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
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: eb75aa53051e7e3c424ffe131cda61324fe86b1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159970"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Одностраничное приложение: Восподписание и выход из системы

Узнайте, как добавить в код для одностраничного приложения.

Прежде чем получить токены для доступа к AИГИЛ в приложении, необходимо проверить подлинность пользовательского контекста. Вы можете войти в число пользователей в своем приложении msAL.js двумя способами:

* [Всплывающее окно](#sign-in-with-a-pop-up-window), с `loginPopup` помощью метода
* [Перенаправить,](#sign-in-with-redirect)с `loginRedirect` помощью метода

Вы также можете по желанию передать области AIS, на которые вам нужно согласие пользователя во время входини.

> [!NOTE]
> Если ваше приложение уже имеет доступ к аутентифицированному контексту пользователя или токену ID, вы можете пропустить шаг входа и непосредственно приобрести токены. Для получения подробной информации [см.](msal-js-sso.md#sso-without-msaljs-login)

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Выбор между всплывающим или перенаправлением опыта

Вы не можете использовать как всплывающие, так и перенаправить методы в приложении. Выбор между всплывающими или перенаправить опыт зависит от потока приложения:

* Если вы не хотите, чтобы пользователи отошли от вашей главной страницы приложения во время проверки подлинности, мы рекомендуем всплывающий метод. Поскольку перенаправление аутентификации происходит в всплывающем окне, состояние основного приложения сохраняется.

* Если у пользователей есть ограничения браузера или политики, где всплывающие окна отключены, можно использовать метод перенаправления. Используйте метод перенаправления с браузером Internet Explorer, потому что есть [известные проблемы с всплывающими окнами на Internet Explorer.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)

## <a name="sign-in-with-a-pop-up-window"></a>Восподписание с всплывающее окно

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

# <a name="angular"></a>[Angular](#tab/angular)

Обертка MSAL Angular позволяет обеспечить определенные `MsalGuard` маршруты в приложении, добавляя к определению маршрута. Этот охранник будет ссылаться на метод, чтобы войти в систему, когда этот маршрут будет достигнут.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Для всплывающих окон, включите опцию конфигурации. `popUp` Вы также можете передать области, требующие согласия следующим образом:

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
---

## <a name="sign-in-with-redirect"></a>Воссоединение с перенаправлением

# <a name="javascript"></a>[Javascript](#tab/javascript)

Методы перенаправления не возвращают обещание из-за отойти от основного приложения. Для обработки и доступа к возвращенным токенам необходимо зарегистрировать обратные вызовы об успехах и ошибках, прежде чем вызвать методы перенаправления.

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

# <a name="angular"></a>[Angular](#tab/angular)

Код здесь такой же, как описано ранее в разделе о входе с всплывающее окно. Поток по умолчанию перенаправывается.

> [!NOTE]
> Токен ID не содержит согласованных областей и представляет только подлинного пользователя. Согласованные области возвращаются в токен доступа, который вы приобретете на следующем этапе.

---

## <a name="sign-out"></a>Функция выхода

Библиотека MSAL предоставляет `logout` метод, который очищает кэш в хранилище браузера и отправляет запрос на выгон в Azure Active Directory (Azure AD). После выхода из системы библиотека по умолчанию перенаправляет обратно на стартовую страницу приложения.

Вы можете настроить URI, к которому он должен `postLogoutRedirectUri`перенаправить после выхода, установив. Этот URI также должен быть зарегистрирован в качестве системы ури входа в вашу заявку.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

# <a name="angular"></a>[Angular](#tab/angular)

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

---

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Получение маркера для приложения](scenario-spa-acquire-token.md)
