---
title: Получение согласия для нескольких ресурсов (библиотека проверки подлинности Майкрософт для .NET) | Azure
description: Узнайте, как пользователь может получить предварительного согласия для нескольких ресурсов, с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: 45bad9a03e8eff6f22ebb99fd2ef4bcd5fecf9b5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158825"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Пользователь получает разрешения для нескольких ресурсов, с помощью MSAL.NET
Конечная точка платформы Microsoft identity не позволяет получить маркер для нескольких ресурсов за один раз. При использовании библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET), параметр области в методе получения токена должны содержать только областей для одного ресурса. Тем не менее, вы можете предварительно дать согласие на наращивание ресурсов выполняется несколько ресурсов, указав дополнительные области с помощью `.WithExtraScopeToConsent` построитель метода.

> [!NOTE]
> Получение согласия для нескольких ресурсов работает для платформы удостоверений Microsoft, но не для Azure AD B2C. Azure AD B2C поддерживает только согласия администратора, не согласия пользователя.

Например при наличии двух ресурсов, которые имеют 2 каждой области:

- https://mytenant.onmicrosoft.com/customerapi (с 2 областей `customer.read` и `customer.write`)
- https://mytenant.onmicrosoft.com/vendorapi (с 2 областей `vendor.read` и `vendor.write`)

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
