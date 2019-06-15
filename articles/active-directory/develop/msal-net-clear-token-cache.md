---
title: Очистить кэш маркеров с помощью библиотеки проверки подлинности Майкрософт для .NET в Azure
description: Узнайте, как очистить кэш маркеров, с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6763c6b2b1f9b4de7d8669a50a4979a7aac00c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544119"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Очистить кэш маркеров, с помощью MSAL.NET

Когда вы [получения маркеров доступа](msal-acquire-cache-tokens.md) с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET), маркер кэшируется. Если приложение требует маркер, следует вначале вызвать `AcquireTokenSilent` метод на наличие допустимого маркера в кэше. 

Очистка кэша достигается за счет удаления учетные записи из кэша. Файл cookie сеанса, находящийся в браузере, не будет удален.  В следующем примере создается экземпляр общедоступного клиентского приложения, получает учетные записи для приложения и удаляет учетные записи.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Чтобы узнать о получении и кэширование маркеров, прочитайте [получения маркеров доступа](msal-acquire-cache-tokens.md).
