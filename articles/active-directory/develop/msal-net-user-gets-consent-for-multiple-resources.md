---
title: Получить согласие на несколько ресурсов (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как пользователь может получить предварительное согласие на несколько ресурсов, используя библиотеку подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 94c9a2b6a46262ad293da9ca3ba493d6f898c870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085840"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Пользователь получает согласие на использование нескольких ресурсов с помощью MSAL.NET
Конечная точка платформы идентификации Майкрософт не позволяет получить токен сразу для нескольких ресурсов. При использовании Библиотеки аутентификации Майкрософт для .NET (MSAL.NET) параметр областей в методе приобретения токенов должен содержать только области для одного ресурса. Тем не менее, можно предварительно дать согласие на несколько `.WithExtraScopeToConsent` ресурсов авансом, указав дополнительные области с помощью метода builder.

> [!NOTE]
> Получение согласия на несколько ресурсов работает для платформы идентификации Майкрософт, но не для Azure AD B2C. Azure AD B2C поддерживает только согласие админа, а не согласие пользователя.

Например, если у вас есть два ресурса, которые имеют 2 области каждый:

- https:\//mytenant.onmicrosoft.com/customerapi (с `customer.read` 2 `customer.write`областями и )
- https:\//mytenant.onmicrosoft.com/vendorapi (с `vendor.read` 2 `vendor.write`областями и )

Следует использовать `.WithExtraScopeToConsent` модификатор, который имеет параметр *extraScopesToConsent,* показанный в следующем примере:

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

Это даст вам токен доступа к первому веб-API. Затем, когда вам нужно получить доступ ко второму веб-API, вы можете бесшумно приобрести токен из кэша токенов:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
