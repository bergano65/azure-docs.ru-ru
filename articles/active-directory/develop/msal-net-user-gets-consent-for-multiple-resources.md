---
title: Получение согласия для нескольких ресурсов (Библиотека проверки подлинности Microsoft для .NET)
titleSuffix: Microsoft identity platform
description: Узнайте, как пользователь может получить предварительное согласие на использование нескольких ресурсов с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3a3d50e94f76341dc349eda997d6e25ca96dec0
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802705"
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
