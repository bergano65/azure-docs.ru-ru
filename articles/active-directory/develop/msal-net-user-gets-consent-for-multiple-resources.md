---
title: Получение согласия для нескольких ресурсов (MSAL.NET) | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как пользователь может получить предварительное согласие на использование нескольких ресурсов с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89afdbed6870b4ce739ed51131def686c41a3015
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921753"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Пользователь получает согласие на использование нескольких ресурсов с помощью MSAL.NET
Конечная точка платформы Microsoft Identity не позволяет получить маркер для нескольких ресурсов одновременно. При использовании библиотеки проверки подлинности Microsoft для .NET (MSAL.NET) параметр областей в методе получения маркера должен содержать только области для одного ресурса. Однако вы можете заранее согласиться с несколькими ресурсами, указав дополнительные области с помощью метода `.WithExtraScopeToConsent` Builder.

> [!NOTE]
> Получение согласия для нескольких ресурсов работает для платформы Microsoft Identity, но не для Azure AD B2C. Azure AD B2C поддерживает только разрешение администратора, а не согласие пользователя.

Например, при наличии двух ресурсов с двумя областями:

- HTTPS:\//mytenant.onmicrosoft.com/customerapi (с 2 областями `customer.read` и `customer.write`)
- HTTPS:\//mytenant.onmicrosoft.com/vendorapi (с 2 областями `vendor.read` и `vendor.write`)

Следует использовать модификатор `.WithExtraScopeToConsent`, который имеет параметр *екстраскопестоконсент* , как показано в следующем примере:

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

Это позволит получить маркер доступа для первого веб-API. Затем, когда вам нужно получить доступ ко второму веб-API, вы можете получить маркер из кэша маркеров без вывода сообщений.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
