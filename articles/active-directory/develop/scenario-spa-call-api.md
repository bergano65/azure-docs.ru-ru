---
title: Одностраничное приложение (вызов веб-API) — платформе Microsoft identity
description: Узнайте, как создать одностраничное приложение (вызов веб-API)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f141a5374c0e794b264f6e0135ca3e15ff8359
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074850"
---
# <a name="single-page-application---call-a-web-api"></a>Одностраничное приложение - вызов в веб-API

Мы рекомендуем вызывать `acquireTokenSilent` метод для получения или обновления маркера доступа перед вызовом веб-API. Получив маркер, можно вызвать защищенный веб-API.

## <a name="call-a-web-api"></a>Вызов веб-API

### <a name="javascript"></a>JavaScript

Используйте маркер доступа, полученные как носителя в HTTP-запрос для вызова веб-API, например Microsoft Graph API. Например: 

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
