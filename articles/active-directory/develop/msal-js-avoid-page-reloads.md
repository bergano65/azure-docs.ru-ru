---
title: Избегайте перезагрузки страниц (MSAL.js) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как избежать перезагрузки страниц при приобретении и обновлении токенов, с помощью библиотеки подлинности Майкрософт для JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 63944a5a9af34c2d4cf98eeb870a730df49654e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084961"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Избегайте перезарядки страниц при приобретении и обновлении токенов с помощью MSAL.js
Библиотека аутентификации Microsoft для JavaScript (MSAL.js) использует скрытые `iframe` элементы для бесшумного приобретения и обновления токенов в фоновом режиме. Azure AD возвращает токен обратно в зарегистрированный redirect_uri, указанный в запросе токенов (по умолчанию это корневая страница приложения). Так как ответ 302, это приводит к `redirect_uri` HTML, соответствующие `iframe`получать загружены в . Обычно приложение `redirect_uri` является корневой страницей, и это заставляет его перезагрузить.

В других случаях, если навигация по корневой странице приложения требует проверки `iframe` подлинности, это может привести к вложенным элементам или `X-Frame-Options: deny` ошибке.

Поскольку MSAL.js не может отклонить 302, выпущенные Azure AD, и `redirect_uri` обязан обрабатывать возвращенный `iframe`токен, он не может предотвратить загрузку в .

Чтобы избежать перезагрузки всего приложения снова или других ошибок, вызванных этим, пожалуйста, следуйте этим обходным пути.

## <a name="specify-different-html-for-the-iframe"></a>Указать различные HTML для iframe

Установите `redirect_uri` свойство на конфигурацию на простую страницу, которая не требует проверки подлинности. Вы должны убедиться, что `redirect_uri` он совпадает с зарегистрированным на портале Azure. Это не повлияет на работу пользователя в области входа, поскольку MSAL сохраняет стартовую страницу, когда пользователь начинает процесс входа, и перенаправляет обратно в точное местоположение после завершения входа.

## <a name="initialization-in-your-main-app-file"></a>Инициализация в основном файле приложения

Если ваше приложение структурировано таким образом, что есть один центральный файл Javascript, который определяет инициализацию приложения, разгром и другие вещи, `iframe` вы можете условно загрузить модули приложения в зависимости от того, загружается ли приложение в или нет. Пример:

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
Подробнее о [создании одностраничного приложения (SPA)](scenario-spa-overview.md) с помощью MSAL.js.