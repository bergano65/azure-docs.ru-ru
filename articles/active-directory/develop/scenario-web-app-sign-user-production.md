---
title: Веб-приложение, где пользователи входа в систему (перемещение в рабочую среду) - платформой Microsoft identity
description: Узнайте, как создать веб-приложение, которое поддерживает вход пользователей (для перемещения в рабочую среду)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074715"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Веб-приложение, которое выполняет вход пользователей - переносу в рабочую среду

Теперь, когда вы знаете, как получить токен для вызова веб-API, узнайте, как переместить его в рабочую среду.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Дальнейшие действия

### <a name="calling-web-apis-scenario"></a>Вызов веб-API-интерфейсы сценарий

Один раз пользователей веб-приложение выполняет вход, он может вызвать веб-API от имени вошедшего в систему пользователей. Вызов веб-API из веб-приложения является объектом следующего сценария:

> [!div class="nextstepaction"]
> [Веб-приложение, которое вызывает веб-API](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>Глубокое погружение, учебник по web app

Дополнительные сведения о других способах входа пользователей в ASP.NET Core учебник: [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Это прогрессивного SQL с помощью рабочего кода все готово для веб-приложения, включая добавление входа.

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
