---
title: Одностраничное приложение (вызов веб-API) — платформа Microsoft Identity
description: Сведения о создании одностраничного приложения (вызов веб-API)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4170a6642d35802581b5d1ff28eb802a6eb3482b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766163"
---
# <a name="single-page-application-call-a-web-api"></a>Одностраничное приложение: вызов веб-API

Рекомендуется вызвать метод `acquireTokenSilent`, чтобы получить или продлить маркер доступа перед вызовом веб-API. После создания маркера можно вызвать защищенный веб-API.

## <a name="call-a-web-api"></a>Вызов веб-API

### <a name="javascript"></a>JavaScript

Используйте полученный маркер доступа в качестве носителя в HTTP-запросе для вызова любого веб-API, например Microsoft Graph API. Пример.

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

### <a name="angular"></a>Angular

Угловая оболочка MSAL использует преимущества перехватчика HTTP для автоматического получения маркеров доступа и их присоединения к API-запросам. Дополнительные сведения см. в разделе [Получение маркера для вызова API](scenario-spa-acquire-token.md).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-spa-production.md)
