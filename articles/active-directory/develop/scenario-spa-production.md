---
title: Перемещение одностраничного приложения в производство - платформа идентификации Майкрософт Azure
description: Узнайте, как создать одностраничное приложение (переход к производству)
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
ms.openlocfilehash: 04a900c98311f4e7dcccbfca93ea41e212308759
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882103"
---
# <a name="single-page-application-move-to-production"></a>Одностраничное приложение: Переход к производству

Теперь, когда вы знаете, как приобрести токен для вызова веб-aIS, узнайте, как перейти к производству.

## <a name="improve-your-app"></a>Улучшение приложения

[Позволяет войти в систему,](msal-logging.md) чтобы подготовить производство приложений.

## <a name="test-your-integration"></a>Проверьте свою интеграцию

Протестируйте интеграцию согласно [контрольному списку для интеграции платформы удостоверений Майкрософт](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Дальнейшие действия

Глубокое погружение образца быстрого запуска, в котором объясняется код, как войти в пользователей и получить токен доступа для вызова Microsoft Graph API с помощью MSAL.js:

> [!div class="nextstepaction"]
> [JavaScript SPA учебник](./tutorial-v2-javascript-spa.md)

Пример, демонстрирующие, как получить токены для собственного бэк-энда web API с помощью MSAL.js:

> [!div class="nextstepaction"]
> [SPA с ASP.NET задней частью](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

Пример, который показывает, как использовать MSAL.js для регистрации в пользователях в приложении, зарегистрированном в Azure Active Directory B2C (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA с Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
