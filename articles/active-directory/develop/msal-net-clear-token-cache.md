---
title: Очистить кэш токенов (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как очистить кэш маркеров с помощью библиотеки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a10efb5ff0a2c6a3ced3631dfe82c86e3e8a72fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084766"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Очистить кэш маркера с помощью MSAL.NET

При [приобретении токена доступа](msal-acquire-cache-tokens.md) с помощью библиотеки подлинности Майкрософт для .NET (MSAL.NET) токен консея кончается. Когда приложению нужен маркер, он `AcquireTokenSilent` должен сначала вызвать метод, чтобы проверить, находится ли приемлемый маркер в кэше. 

Очистка кэша достигается путем удаления учетных записей из кэша. Файл cookie сеанса, находящийся в браузере, не будет удален.  Следующий пример мгновенно выполняет публичное клиентское приложение, получает учетные записи для приложения и удаляет учетные записи.

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

Чтобы узнать больше о приобретении и кэширования токенов, прочитайте [приобрести токен доступа](msal-acquire-cache-tokens.md).
