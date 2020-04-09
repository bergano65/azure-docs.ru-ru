---
title: Перемещение веб-приложения, которое подписывает в пользователях к производству - Microsoft идентификационная платформа (ru) Azure
description: Узнайте, как создать веб-приложение, которое подписывает в пользователях (переход к производству)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9c5fd444c55a20441325088912a07eb051219b84
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881474"
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
> [Веб-приложение, которое вызывает веб-API](scenario-web-app-call-api-overview.md)

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
