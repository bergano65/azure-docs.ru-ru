---
title: Одностраничное приложение (вызов веб-API) — платформе Microsoft identity
description: Узнайте, как создать одностраничное приложение (вызов веб-API)
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
ms.openlocfilehash: 77a4ed01ac55a1153a62c672b33056a543b912ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545614"
---
# <a name="single-page-application---call-a-web-api"></a>Одностраничное приложение - вызов в веб-API

Мы рекомендуем вызывать `acquireTokenSilent` метод для получения или обновления маркера доступа перед вызовом веб-API. Получив маркер, можно вызвать защищенный веб-API.

## <a name="call-a-web-api"></a>Вызов веб-API

### <a name="javascript"></a>JavaScript

Используйте маркер доступа, полученные как носителя в HTTP-запрос для вызова веб-API, например Microsoft Graph API. Пример:

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

Как упоминалось в [получение маркеров разделе](scenario-spa-acquire-token.md), MSAL Angular оболочки использует перехватчик HTTP для автоматического получения маркеров доступа без уведомления и присоединить их к HTTP-запросов к API.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-spa-production.md)
