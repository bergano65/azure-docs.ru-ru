---
title: Приложения на рабочем столе, вызовы веб-API-интерфейсы (вызов веб-API) — платформе Microsoft identity
description: Узнайте, как создание классического приложения, которое вызывает веб-API (вызов веб-API)
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
ms.openlocfilehash: 6fb240b54c3d698ead9d3427f603acca2b53745a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075930"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Классическое приложение, которое вызывает веб-API - вызов веб-API

Теперь, когда у вас есть маркер, можно вызвать защищенный веб-API.

## <a name="calling-a-web-api-from-net"></a>Вызов веб-API из .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Вызов несколько интерфейсов API - добавочное согласие и условного доступа

Если необходимо вызвать несколько интерфейсов API для одного пользователя, когда вы получили маркер для первый API, можно просто вызвать `AcquireTokenSilent`, и вы получите токен для других API-интерфейсы автоматически большую часть времени.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Случаи, где требуется взаимодействие — когда:

- Пользователь дал согласие для первый API, но теперь потребуется предоставить согласие для нескольких областей (добавочное согласие пользователя)
- Первый API не требуются несколько двухфакторной проверки подлинности, но не следующего.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-desktop-production.md)
