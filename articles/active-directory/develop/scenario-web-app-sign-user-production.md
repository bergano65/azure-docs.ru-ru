---
title: Веб-приложение, которое входит в систему пользователей (переместить в рабочую среду) — платформа Microsoft Identity
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
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086551"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Веб-приложение, которое входит в систему пользователей — перейти в рабочую среду

Теперь, когда вы узнали, как получить маркер для вызова веб-API, Узнайте, как переместить его в рабочую среду.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Следующие шаги

### <a name="calling-web-apis-scenario"></a>Вызов сценария веб-API

После входа в веб-приложение пользователи могут вызывать веб-API от имени вошедших в систему пользователей. Вызов веб-API из веб-приложения является объектом следующего сценария:

> [!div class="nextstepaction"]
> [Веб-приложение, которое вызывает веб-API](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>Руководство по веб-приложениям для глубокого углубления ASP.NET Core

Узнайте о других способах входа пользователей с помощью ASP.NET Core учебнике [MS-Identity-aspnetcore-webapp-Tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Этот пример представляет собой прогрессивный учебник с кодом готовности к работе для веб-приложения, включая добавление входа с учетными записями в:

- Ваша организация,
- несколько организаций,
- рабочие или учебные учетные записи или персональные учетная запись Майкрософт
- с [Azure AD B2C](https://aka.ms/aadb2c)
- или в национальных облаках.

### <a name="sample-code---java-web-app"></a>Пример кода — веб-приложение Java

Дополнительные сведения о веб-приложении Java см. в примере на сайте GitHub: [Веб-приложение Java, которое входит в систему пользователей с платформой идентификации Майкрософт и вызывает Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
