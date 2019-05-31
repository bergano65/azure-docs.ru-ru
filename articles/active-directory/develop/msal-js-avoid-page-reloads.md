---
title: Избежать перезагрузок страницы (библиотека проверки подлинности Майкрософт для JavaScript) | Azure
description: Узнайте, как избежать перезагрузок страницы, при получении и обновления существующего маркеры, автоматически с помощью библиотеки проверки подлинности Майкрософт для JavaScript (MSAL.js).
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
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 162811221e6dde89ad11f358b2ec8f32f3c82522
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420473"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Избежать перезагрузок страницы при получении и обновления существующего маркеры, автоматически, используя MSAL.js
Библиотека проверки подлинности Майкрософт для JavaScript (MSAL.js) используется скрытые `iframe` элементов для получения и обновления маркеров автоматически в фоновом режиме. Azure AD возвращает маркер обратно в зарегистрированных URI перенаправления, указанный в запросе токена (по умолчанию это корень странице приложения). Так как ответ 302, это приводит к HTML, соответствующий `redirect_uri` загружаются в `iframe`. Обычно приложения `redirect_uri` на корневую страницу и это вызывает перезагрузку.

В других случаях, если переход к корневой странице приложения требует проверки подлинности, может привести к вложенной `iframe` элементов или `X-Frame-Options: deny` ошибки.

Поскольку MSAL.js не удалось отменить 302, выданный Azure AD и обработки возвращаемый маркер, она не может предотвратить `redirect_uri` из загружаются в `iframe`.

Чтобы избежать перезагрузки снова всего приложения или других ошибок, возникающих из-за этого, выполните следующие обходные пути.

## <a name="specify-different-html-for-the-iframe"></a>Укажите другой HTML для iframe

Задать `redirect_uri` свойства конфигурации для простую страницу, которая не требует проверки подлинности. Необходимо убедиться, что он совпадает с `redirect_uri` зарегистрированных на портале Azure. Это не повлияет на вход пользователя как MSAL сохраняет начальной страницы, когда пользователь начинает процесс входа в систему и перенаправляет обратно в расположение, после завершения входа.

## <a name="initialization-in-your-main-app-file"></a>Инициализация в файле основного приложения

Если приложения упорядочено таким образом, есть один центральный файл Javascript, который определяет инициализации приложения, маршрутизацию и прочее, ее можно загружать по условию вашей модулей приложения, в зависимости от того, является ли приложение загружается в `iframe` или нет. Пример:

В AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

В Angular: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о [построение одностраничного приложения (SPA)](scenario-spa-overview.md) с помощью MSAL.js.