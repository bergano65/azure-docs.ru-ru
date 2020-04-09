---
title: Создайте одностраничное приложение, вызывая веб-API - платформу идентификации Microsoft Azure
description: Узнайте, как создать одностраничное приложение, которое вызывает web API
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 5b70b109f43e80fc3ec68f52aef2dba6823033bb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882137"
---
# <a name="single-page-application-call-a-web-api"></a>Одностраничное приложение: Вызов веб-API

Мы рекомендуем вам `acquireTokenSilent` вызвать метод приобретения или обновления токена доступа, прежде чем вызвать веб-API. После того, как у вас есть маркер, вы можете вызвать защищенный веб-API.

## <a name="call-a-web-api"></a>Вызов веб-API

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
