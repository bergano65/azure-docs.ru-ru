---
title: Веб-приложение, которое входит в систему пользователей (переходит в рабочую среду) — платформа Microsoft Identity
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: efcc7ed4f80d0e3e9750d19ff95d010052a08b87
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481877"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Веб-приложение, которое входит в систему пользователей: перейти в рабочую среду

Теперь, когда вы узнали, как получить маркер для вызова веб-API, Узнайте, как переместить его в рабочую среду.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Дополнительная информация

### <a name="scenario-for-calling-web-apis"></a>Сценарий для вызова веб-API

После того как веб-приложение подписывает пользователей, оно может вызывать веб-API от имени вошедших в систему пользователей. Вызов веб-API из веб-приложения является объектом следующего сценария:

> [!div class="nextstepaction"]
> [Веб-приложение, которое вызывает веб-API](scenario-web-app-call-api-overview.md)

### <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Глубокое углубление: учебник по веб-приложениям ASP.NET Core

Узнайте о других способах входа пользователей с помощью этого ASP.NET Core учебнике: 

> [!div class="nextstepaction"]
> [Разрешите веб-приложениям выполнять вход пользователей и вызывать API с помощью платформы удостоверений Майкрософт для разработчиков](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

В этом последовательном руководстве есть готовый к использованию код для веб-приложения, включая добавление учетных записей в:

- Ваша организация
- Несколько организаций
- Рабочие или учебные учетные записи или личные учетные записи Майкрософт
- [Azure AD B2C](https://aka.ms/aadb2c)
- Местные облака

### <a name="sample-code-java-web-app"></a>Пример кода: веб-приложение Java

Дополнительные сведения о веб-приложении Java из этого примера на сайте GitHub: 

> [!div class="nextstepaction"]
> [Веб-приложение Java, которое входит в систему пользователей с платформой идентификации Майкрософт и вызывает Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
