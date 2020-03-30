---
title: Создайте одностраничное приложение, вызывая веб-API - платформу идентификации Microsoft Azure
description: Узнайте, как создать одностраничное приложение, которое вызывает web API
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: ccece6c840033913ec6d96b446dcb98c4befb32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160004"
---
# <a name="single-page-application-call-a-web-api"></a>Одностраничное приложение: Вызов веб-API

Мы рекомендуем вам `acquireTokenSilent` вызвать метод приобретения или обновления токена доступа, прежде чем вызвать веб-API. После того, как у вас есть маркер, вы можете вызвать защищенный веб-API.

## <a name="call-a-web-api"></a>Вызов веб-API

# <a name="javascript"></a>[Javascript](#tab/javascript)

Используйте приобретенный токен доступа в качестве носителя в запросе HTTP для вызова любого веб-API, например Microsoft Graph API. Пример:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

Обертка MSAL Angular использует перехватчик HTTP, чтобы автоматически получать токены доступа и прикреплять их к запросам HTTP к AIS. Для получения дополнительной информации [см. Приобрести токен для вызова API.](scenario-spa-acquire-token.md)

---

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Перенос в рабочую среду](scenario-spa-production.md)
