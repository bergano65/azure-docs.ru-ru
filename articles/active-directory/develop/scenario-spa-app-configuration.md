---
title: Настройка одностраничного приложения - идентификационная платформа Майкрософт Azure
description: Узнайте, как создать одностраничное приложение (конфигурация кода приложения)
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882341"
---
# <a name="single-page-application-code-configuration"></a>Одностраничное приложение: конфигурация кода

Узнайте, как настроить код для одностраничного приложения (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>Библиотеки MSAL, поддерживающие неявный поток

Платформа идентификации Майкрософт предоставляет следующие библиотеки библиотеки подлинности Майкрософт (MSAL) для поддержки неявного потока с помощью рекомендуемых в отрасли методов безопасности:

| Библиотека MSAL | Описание |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Простая библиотека JavaScript для использования в любом веб-приложении на стороне клиента, построенном с помощью рамок JavaScript или SPA, таких как Angular, Vue.js и React.js. |
| ![MSAL Угловой](media/sample-v2-code/logo_angular.png) <br/> [MSAL Угловой](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Обертка из основной библиотеки MSAL.js для упрощения использования в одностраничных приложениях, которые построены через рамки Angular. |

## <a name="application-code-configuration"></a>Конфигурация кода приложения

В библиотеке MSAL регистрационная информация приложения передается в виде конфигурации во время инициализации библиотеки.

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

Для получения дополнительной информации о [Initializing application with MSAL.js](msal-js-initializing-client-applications.md)настраиваемых опциях см.

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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Функции входа и выхода](scenario-spa-sign-in.md)
