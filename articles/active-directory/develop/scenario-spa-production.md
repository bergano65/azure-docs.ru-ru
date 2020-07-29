---
title: Перемещение одностраничного приложения в рабочую среду — платформа Microsoft Identity | Службы
description: Узнайте, как создать одностраничное приложение (переместить в рабочую среду)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 777f3de8f2872e378fe30cc50ee0a5eb3823a625
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82900315"
---
# <a name="single-page-application-move-to-production"></a>Одностраничное приложение: переход в рабочую среду

Теперь, когда вы узнали, как получить маркер для вызова веб-API, Узнайте, как перейти в рабочую среду.

## <a name="improve-your-app"></a>Улучшение приложения

[Включите ведение журнала](msal-logging.md) , чтобы подготовить приложение к работе.

## <a name="test-your-integration"></a>Тестирование интеграции

Протестируйте интеграцию согласно [контрольному списку для интеграции платформы удостоверений Майкрософт](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Дальнейшие шаги

Подробное описание примера краткого руководства, в котором объясняется, как войти в систему пользователей и получить маркер доступа для вызова Microsoft Graph API с помощью MSAL.js.

> [!div class="nextstepaction"]
> [Руководство по JavaScript SPA](./tutorial-v2-javascript-spa.md)

Пример, демонстрирующий получение маркеров для собственного серверного веб-API с помощью MSAL.js:

> [!div class="nextstepaction"]
> [SPA с серверной стороны ASP.NET](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)

Пример, в котором показано, как использовать MSAL.js для входа пользователей в приложение, зарегистрированное в Azure Active Directory B2C (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA с Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
