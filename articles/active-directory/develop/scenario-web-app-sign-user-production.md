---
title: Перемещение веб-приложения, которое подписывает в пользователях к производству - Microsoft идентификационная платформа (ru) Azure
description: Узнайте, как создать веб-приложение, которое подписывает в пользователях (переход к производству)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768108"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Веб-приложение, которое подписывает в пользователях: Переход к производству

Теперь, когда вы знаете, как получить маркер для вызова веб-AIS, узнать, как переместить его в производство.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Дальнейшие действия

### <a name="same-site"></a>Тот же сайт

Убедитесь, что вы понимаете возможные проблемы с новыми версиями браузера Chrome

> [!div class="nextstepaction"]
> [Как обрабатывать изменения файлов cookie SameSite в браузере Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Сценарий вызова веб-AIS

После того, как ваше веб-приложение подпишется в пользователях, оно может вызывать веб-аГИТ от имени пользователей, вписающихся в него. Вызов веб-AIS из веб-приложения является объектом следующего сценария:

> [!div class="nextstepaction"]
> [Веб-приложение, которое вызывает веб-AIS](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Глубокое погружение: ASP.NET учебник по веб-приложению Core

Узнайте о других способах регистрации пользователей с помощью этого ASP.NET учебника Core: 

> [!div class="nextstepaction"]
> [Включите веб-приложения для регистрации пользователей и вызов AI с платформой идентификации Майкрософт для разработчиков](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Этот прогрессивный учебник имеет готовый к производству код для веб-приложения, в том числе, как добавить ввод со счетами в:

- Ваша организация
- Несколько организаций
- Рабочие или школьные учетные записи, или личные учетные записи Майкрософт
- [Azure AD B2C](https://aka.ms/aadb2c)
- Национальные облака

## <a name="sample-code-java-web-app"></a>Пример кода: Веб-приложение Java

Узнайте больше о веб-приложении Java из этого примера на GitHub: 

> [!div class="nextstepaction"]
> [Приложение Java Web, которое подписывает пользователей с платформой идентификации Майкрософт и вызывает Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
