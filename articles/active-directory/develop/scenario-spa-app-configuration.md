---
title: Одностраничное приложение (конфигурация кода приложения) — платформа Microsoft Identity
description: Сведения о создании одностраничного приложения (конфигурация кода приложения)
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
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891528"
---
# <a name="single-page-application---code-configuration"></a>Одностраничная Конфигурация приложения с одним кодом

Узнайте, как настроить код для одностраничного приложения (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Библиотеки MSAL, поддерживающие неявный поток

Платформа Microsoft Identity предоставляет библиотеку MSAL. js для поддержки неявного потока с использованием рекомендуемых в отрасли рекомендаций по обеспечению безопасности.  

Библиотеки, поддерживающие неявный поток:

| Библиотека MSAL | Описание |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Простая библиотека JavaScript для использования в любом веб-приложении на стороне клиента, созданном на основе таких платформ JavaScript или SPA, как угловой, Vue. js, реагируют. js и т. д. |
| ![MSALный угловой](media/sample-v2-code/logo_angular.png) <br/> [MSALный угловой](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Оболочка основной библиотеки MSAL. js для упрощения использования в одностраничных приложениях, созданных с помощью угловой платформы. Эта библиотека находится на этапе предварительной версии и содержит [Известные проблемы](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) с определенными версиями и браузерами. |

## <a name="application-code-configuration"></a>Конфигурация кода приложения

В библиотеке MSAL сведения о регистрации приложения передаются как конфигурация во время инициализации библиотеки.

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
Дополнительные сведения о доступных настраиваемых параметрах см. в разделе [Инициализация приложения с помощью MSAL. js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Вход и выход](scenario-spa-sign-in.md)
