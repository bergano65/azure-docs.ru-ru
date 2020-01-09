---
title: Очистить кэш маркеров (MSAL.NET) | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как очистить кэш маркеров с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11ff07e551002b8a0242afe51f3868a2f4a0ee85
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424259"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Очистка кэша маркеров с помощью MSAL.NET

При [получении маркера доступа](msal-acquire-cache-tokens.md) с помощью библиотеки проверки подлинности Майкрософт для .net (MSAL.NET) маркер кэшируется. Когда приложению требуется токен, сначала необходимо вызвать метод `AcquireTokenSilent`, чтобы проверить, находится ли допустимый маркер в кэше. 

Очистка кэша достигается путем удаления учетных записей из кэша. Файл cookie сеанса, находящийся в браузере, не будет удален.  Следующий пример создает экземпляр общедоступного клиентского приложения, получает учетные записи для приложения и удаляет учетные записи.

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

Дополнительные сведения о получении и кэшировании токенов см. в статье [Получение маркера доступа](msal-acquire-cache-tokens.md).
