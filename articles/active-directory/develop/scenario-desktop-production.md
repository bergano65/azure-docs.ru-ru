---
title: Перемещение настольного приложения вызова веб-AIS в производство - Microsoft идентификационная платформа (ru) Azure
description: Узнайте, как переместить настольное приложение, которое вызывает веб-AIS в производство
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea564eb69f102d8e548bf8ae9a626598fa264cd4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882885"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Настольное приложение, которое вызывает веб-AIS: Переход к производству

В этой статье вы узнаете, как переместить настольное приложение, которое вызывает web-aIS в производство.

## <a name="handle-errors-in-desktop-applications"></a>Обработка ошибок в настольных приложениях

В различных потоках вы научились обрабатывать ошибки для тихих потоков, как показано в фрагментах кода. Вы также видели, что есть случаи, когда взаимодействие необходимо, как при поэтапном согласии и условном доступе.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Попросите пользователя дать согласие авансом на несколько ресурсов

> [!NOTE]
> Получение согласия на несколько ресурсов работает для платформы идентификации Майкрософт, но не для Azure Active Directory (Azure AD) B2C. Azure AD B2C поддерживает только согласие админа, а не согласие пользователя.

Вы не можете получить токен для нескольких ресурсов одновременно с платформой идентификации Майкрософт (v2.0) в конечном пункте. Параметр `scopes` может содержать области только для одного ресурса. Вы можете убедиться, что пользователь предварительно соглашается `extraScopesToConsent` на несколько ресурсов, используя параметр.

Например, у вас может быть два ресурса с двумя областями:

- `https://mytenant.onmicrosoft.com/customerapi`с прицелами `customer.read` и`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`с прицелами `vendor.read` и`vendor.write`

В этом примере `.WithAdditionalPromptToConsent` используйте модификатор, который имеет `extraScopesToConsent` параметр.

например

### <a name="in-msalnet"></a>В MSAL.NET

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

### <a name="in-msal-for-ios-and-macos"></a>В MSAL для iOS и macOS

Objective-C.

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Этот вызов получает токен доступа к первому веб-API.

Если вам нужно позвонить во второй `AcquireTokenSilent` веб-API, позвоните в API.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Личный аккаунт Майкрософт требует повторного согласия при каждом запуске приложения

Для пользователей личных учетных записей Майкрософт, повторение согласия на каждого родного клиента (настольное или мобильное приложение) вызова для авторизации является предполагаемым поведением. Идентификация родного клиента по своей сути небезопасна, что противоречит конфиденциальной идентификации клиента. Конфиденциальные клиентские приложения обмениваются секретом с платформой Microsoft Identity, чтобы доказать свою личность. Платформа идентификации Майкрософт решила смягчить эту незащищенность для служб потребителей, побуждая пользователя к согласию каждый раз, когда приложение авторизовано.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
