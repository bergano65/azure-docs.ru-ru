---
title: Одностраничное приложение (конфигурации кода приложения) — платформе Microsoft identity
description: Узнайте, как создать одностраничное приложение (конфигурации кода приложения)
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
ms.openlocfilehash: b71454fc553a0f81c26426a6a9588f15d5311e38
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406434"
---
# <a name="single-page-application---code-configuration"></a>Одностраничное приложение - код конфигурации

Узнайте, как настроить код для одностраничного приложения (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Библиотеки MSAL, которые поддерживают неявный поток

MSAL.js библиотеки для поддержки неявный поток, с помощью отрасли Рекомендуемые приемы безопасного предоставляет платформе Microsoft identity.  

Ниже приведены библиотеки, которые поддерживают неявный поток.

| Библиотека MSAL | Описание |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Обычная библиотека JavaScript для использования в любой веб-приложения на стороне клиента, созданные с помощью JavaScript или SPA платформы, такие как Angular, Vue.js, React.js и т. д. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Программа-оболочка MSAL.js основной библиотеки для упрощения использования в одностраничные приложения, созданные с помощью Angular framework. Эта библиотека доступна в предварительной версии и имеет [известные проблемы](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) с определенных версий Angular и браузеров. |

## <a name="application-code-configuration"></a>Настройка кода приложения

В библиотеке MSAL сведениях о регистрации приложения передается в качестве конфигурации во время инициализации библиотеки.

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
Дополнительные сведения о настраиваемых параметрах, доступных, см. в разделе [инициализация приложения с MSAL.js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientId: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Вход и выход из него](scenario-spa-sign-in.md)
