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
ms.openlocfilehash: 30a9b710ffbf98ebc523217a3b8a7fd9a2640c49
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443047"
---
# <a name="single-page-application-code-configuration"></a>Одностраничное приложение: конфигурация кода

Узнайте, как настроить код для одностраничного приложения (SPA).

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>Библиотеки MSAL для одностраничные приложения и поддерживаемые потоки проверки подлинности

Платформа удостоверений Майкрософт предоставляет следующую библиотеку проверки подлинности Майкрософт для JavaScript (MSAL.js) для поддержки потока неявных потоков и кода авторизации с помощью PKCE, используя Рекомендуемые отраслевые методики обеспечения безопасности:

| Библиотека MSAL | Поток | Описание |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | Поток кода авторизации (PKCE) | Простая библиотека JavaScript для использования в любом веб-приложении на стороне клиента, созданном с помощью платформ JavaScript или SPA, таких как угловой, Vue.js и React.js. |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | Неявный поток | Простая библиотека JavaScript для использования в любом веб-приложении на стороне клиента, созданном с помощью платформ JavaScript или SPA, таких как угловой, Vue.js и React.js. |
| ![MSALный угловой](media/sample-v2-code/logo_angular.png) <br/> [MSALный угловой](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Неявный поток | Обертка основной библиотеки MSAL.js для упрощения использования в одностраничных приложениях, созданных с помощью угловой платформы. |

## <a name="application-code-configuration"></a>Конфигурация кода приложения

В библиотеке MSAL сведения о регистрации приложения передаются как конфигурация во время инициализации библиотеки.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

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

## <a name="next-steps"></a>Дальнейшие действия

Перейдите к следующей статье в этом сценарии: [Вход и](scenario-spa-sign-in.md)выход.
