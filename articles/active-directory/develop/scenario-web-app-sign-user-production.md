---
title: Перемещение веб-приложения, которое выполняет вход пользователей в рабочую среду — платформа Microsoft Identity | Службы
description: Узнайте, как создать веб-приложение, которое входит в систему пользователей (переходит в рабочую среду).
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
ms.openlocfilehash: 47a45b52ac10a44b6efd54c41b3fec1e61a47a35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82181636"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Веб-приложение, которое входит в систему пользователей: перейти в рабочую среду

Теперь, когда вы узнали, как получить маркер для вызова веб-API, Узнайте, как переместить его в рабочую среду.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Дальнейшие действия

### <a name="troubleshooting"></a>Устранение неполадок

> [!NOTE]
> Когда пользователи впервые входят в веб-приложение, им необходимо предоставить согласие. Однако в некоторых организациях пользователи могут видеть примерно следующее сообщение:
>
> *Приложению AppName нужны разрешения на доступ к ресурсам в вашей организации, которые может предоставить только администратор. Попросите администратора предоставить разрешение для этого приложения, прежде чем его можно будет использовать.*
>
> Это связано с тем, что администратор клиента **отключил** возможность пользователей принять согласие. В этом случае вам нужно обратиться к администраторам клиента, чтобы они выводили согласие администратора для областей, необходимых для приложения.

### <a name="same-site"></a>Тот же сайт

Убедитесь, что вы понимаете возможные проблемы с новыми версиями браузера Chrome.

> [!div class="nextstepaction"]
> [Как управлять изменениями файлов cookie SameSite в браузере Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

Пакет Microsoft. Identity. Web NuGet обрабатывает наиболее распространенные проблемы SameSite.

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
- [Azure AD B2C](https://aka.ms/aadb2c)
- Национальные облака

## <a name="sample-code-java-web-app"></a>Пример кода: веб-приложение Java

Дополнительные сведения о веб-приложении Java из этого примера на сайте GitHub: 

> [!div class="nextstepaction"]
> [Веб-приложение Java, которое входит в систему пользователей с платформой идентификации Майкрософт и вызывает Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
