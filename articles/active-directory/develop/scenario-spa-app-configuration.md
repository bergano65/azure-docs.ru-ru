---
title: Настройка одностраничного приложения — платформа Microsoft Identity | Службы
description: Сведения о создании одностраничного приложения (конфигурация кода приложения)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f159105046231ba5fb4e458cdd70d930a411a920
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882341"
---
# <a name="single-page-application-code-configuration"></a>Одностраничное приложение: конфигурация кода

Узнайте, как настроить код для одностраничного приложения (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>Библиотеки MSAL, поддерживающие неявные потоки

Платформа удостоверений Майкрософт предоставляет следующие библиотеки библиотеки проверки подлинности (MSAL) для поддержки неявного потока с помощью рекомендуемых в отрасли методов обеспечения безопасности:

| Библиотека MSAL | Описание |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Простая библиотека JavaScript для использования в любом веб-приложении на стороне клиента, созданном с помощью платформ JavaScript или SPA, таких как угловой, Vue.js и React.js. |
| ![MSALный угловой](media/sample-v2-code/logo_angular.png) <br/> [MSALный угловой](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Обертка основной библиотеки MSAL.js для упрощения использования в одностраничных приложениях, созданных с помощью угловой платформы. |

## <a name="application-code-configuration"></a>Конфигурация кода приложения

В библиотеке MSAL сведения о регистрации приложения передаются как конфигурация во время инициализации библиотеки.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Дополнительные сведения о настраиваемых параметрах см. в разделе [Инициализация приложения с помощью MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Функции входа и выхода](scenario-spa-sign-in.md)
