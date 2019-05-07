---
title: Одностраничное приложение (для перемещения в рабочую среду) - платформой Microsoft identity
description: Узнайте, как создать одностраничное приложение (перемещение в рабочую среду)
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
ms.openlocfilehash: e6a04f5d62ec750cfbe44765e833552bd694654d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075015"
---
# <a name="single-page-application---move-to-production"></a>Одностраничное приложение - перемещения в рабочую среду

Теперь, когда вы знаете, как получить токен для вызова веб-API, узнайте, как для перемещения в рабочую среду.

## <a name="improve-your-app"></a>Улучшить приложение

Выполните шаги, необходимые для подготовки к рабочие приложения.

- [Включение ведения журнала](msal-logging.md) в приложении.

## <a name="test-your-integration"></a>Проверили интеграцию

- Проверили интеграцию, следуя [контрольный список для интеграции платформы удостоверений Microsoft](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены несколько других примеров и учебников.

- Подробный обзор быстрого запуска пример, где объясняется код для входа в систему и получить маркер доступа для вызова API Graph MS, с помощью MSAL.js

    > [!div class="nextstepaction"]
    > [Учебник одностраничного приложения JavaScript](./tutorial-v2-javascript-spa.md)

- Пример, демонстрирующий, как получить маркеры для собственных веб-API серверной части, с помощью MSAL.js

     > [!div class="nextstepaction"]
     > [SPA с сервера ASP.NET](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- Образец, показывающий, как использовать MSAL.js для входа пользователей в приложение зарегистрировано в Azure AD B2C

    > [!div class="nextstepaction"]
    > [SPA с помощью Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
