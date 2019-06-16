---
title: Получение согласия для нескольких ресурсов (библиотека проверки подлинности Майкрософт для .NET) | Azure
description: Узнайте, как пользователь может получить предварительного согласия для нескольких ресурсов, с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
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
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8bd9a86d5ec0d39a7f1c26adac52f41e6420283
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121984"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Пользователь получает разрешения для нескольких ресурсов, с помощью MSAL.NET
Конечная точка платформы Microsoft identity не позволяет получить маркер для нескольких ресурсов за один раз. При использовании библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET), параметр области в методе получения токена должны содержать только областей для одного ресурса. Тем не менее, вы можете предварительно дать согласие на наращивание ресурсов выполняется несколько ресурсов, указав дополнительные области с помощью `.WithExtraScopeToConsent` построитель метода.

> [!NOTE]
> Получение согласия для нескольких ресурсов работает для платформы удостоверений Microsoft, но не для Azure AD B2C. Azure AD B2C поддерживает только согласия администратора, не согласия пользователя.

Например при наличии двух ресурсов, которые имеют 2 каждой области:

- https:\//mytenant.onmicrosoft.com/customerapi (с 2 областей `customer.read` и `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (с 2 областей `vendor.read` и `vendor.write`)

Следует использовать `.WithExtraScopeToConsent` модификатор, который имеет *extraScopesToConsent* параметра, как показано в следующем примере:

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

Вы получите маркер доступа для первого веб-API. Затем когда необходимо получить доступ к второй веб-API, вы можете автоматически получить маркер из кэша маркера:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
