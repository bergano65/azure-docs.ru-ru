---
title: Перемещение одностраничного приложения в рабочую среду — платформа Microsoft Identity | Службы
description: Узнайте, как создать одностраничное приложение (переместить в рабочую среду)
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
ms.openlocfilehash: ba41b29cfcea2d9dfcab2cd552040eaffa4e90ff
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965285"
---
# <a name="single-page-application-move-to-production"></a>Одностраничное приложение: переход в рабочую среду

Теперь, когда вы узнали, как получить маркер для вызова веб-API, Узнайте, как перейти в рабочую среду.

## <a name="improve-your-app"></a>Улучшение приложения

[Включите ведение журнала](msal-logging.md) , чтобы подготовить приложение к работе.

## <a name="test-your-integration"></a>Тестирование интеграции

Протестируйте интеграцию согласно [контрольному списку для интеграции платформы удостоверений Майкрософт](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Дальнейшие действия

Подробное описание примера краткого руководства, в котором объясняется, как войти в систему пользователей и получить маркер доступа для вызова API Microsoft Graph с помощью MSAL. js:

> [!div class="nextstepaction"]
> [Руководство по JavaScript SPA](./tutorial-v2-javascript-spa.md)

Пример, демонстрирующий получение маркеров для собственного серверного веб-API с помощью MSAL. js:

> [!div class="nextstepaction"]
> [SPA с серверной службой ASP.NET](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

Пример, демонстрирующий использование MSAL. js для входа пользователей в приложение, зарегистрированное в Azure Active Directory B2C (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA с Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
