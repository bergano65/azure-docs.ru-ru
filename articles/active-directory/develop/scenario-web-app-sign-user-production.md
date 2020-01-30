---
title: Перемещение веб-приложения, которое выполняет вход пользователей в рабочую среду — платформа Microsoft Identity | Службы
description: Узнайте, как создать веб-приложение, которое входит в систему пользователей (переходит в рабочую среду).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6c486e59f32afd09a9934ae2298172ccb4ee2414
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768108"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Веб-приложение, которое входит в систему пользователей: перейти в рабочую среду

Теперь, когда вы узнали, как получить маркер для вызова веб-API, Узнайте, как переместить его в рабочую среду.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Дальнейшие действия

### <a name="same-site"></a>Тот же сайт

Убедитесь, что вы понимаете возможные проблемы с новыми версиями браузера Chrome.

> [!div class="nextstepaction"]
> [Как управлять изменениями файлов cookie SameSite в браузере Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Сценарий для вызова веб-API

После того как веб-приложение подписывает пользователей, оно может вызывать веб-API от имени вошедших в систему пользователей. Вызов веб-API из веб-приложения является объектом следующего сценария:

> [!div class="nextstepaction"]
> [Веб-приложение, которое вызывает веб-API](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Глубокое углубление: учебник по веб-приложениям ASP.NET Core

Узнайте о других способах входа пользователей с помощью этого ASP.NET Core учебнике: 

> [!div class="nextstepaction"]
> [Разрешите веб-приложениям выполнять вход пользователей и вызывать API с помощью платформы удостоверений Майкрософт для разработчиков](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

В этом последовательном руководстве есть готовый к использованию код для веб-приложения, включая добавление учетных записей в:

- Ваша организация
- Несколько организаций
- Рабочие или учебные учетные записи или личные учетные записи Майкрософт
- [Azure AD B2C](https://aka.ms/aadb2c)
- Местные облака

## <a name="sample-code-java-web-app"></a>Пример кода: веб-приложение Java

Дополнительные сведения о веб-приложении Java из этого примера на сайте GitHub: 

> [!div class="nextstepaction"]
> [Веб-приложение Java, которое входит в систему пользователей с платформой идентификации Майкрософт и вызывает Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
