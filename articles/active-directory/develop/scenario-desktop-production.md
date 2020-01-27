---
title: Перемещение классического приложения, вызывающего веб-API, в рабочую среду — платформа Microsoft Identity | Службы
description: Узнайте, как переместить классическое приложение, вызывающее веб-API, в рабочую среду.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c8a9cf0c05d8af14d52bb1efb536dc8bbe7db84d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702136"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Классическое приложение, вызывающее веб-API: переместить в рабочую среду

Из этой статьи вы узнаете, как переместить классическое приложение, которое вызывает веб-API, в рабочую среду.

## <a name="handle-errors-in-desktop-applications"></a>Обработку ошибок в приложениях для настольных систем

В разных потоках вы узнали, как обрабатывались ошибки для безошибочных потоков, как показано в фрагментах кода. Вы также видели случаи, когда требуется взаимодействие, как при добавочном согласии и условном доступе.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Получите согласие пользователя на получение нескольких ресурсов

> [!NOTE]
> Получение согласия для нескольких ресурсов работает для платформы Microsoft Identity, но не для Azure Active Directory (Azure AD) B2C. Azure AD B2C поддерживает только разрешение администратора, а не согласие пользователя.

Невозможно получить маркер для нескольких ресурсов одновременно с конечной точкой платформы Microsoft Identity Platform (v 2.0). Параметр `scopes` может содержать области только для одного ресурса. Вы можете убедиться, что пользователь заранее предоставил несколько ресурсов с помощью параметра `extraScopesToConsent`.

Например, у вас может быть два ресурса с двумя областями:

- `https://mytenant.onmicrosoft.com/customerapi` с областями `customer.read` и `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` с областями `vendor.read` и `vendor.write`

В этом примере используйте модификатор `.WithAdditionalPromptToConsent` с параметром `extraScopesToConsent`.

Например:

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

Этот вызов получает маркер доступа для первого веб-API.

Если необходимо вызвать второй веб-API, вызовите API `AcquireTokenSilent`.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>При каждом запуске приложения личная учетная запись Майкрософт требует согласия пользователя

Для пользователей личных учетных записей Майкрософт необходимо повторно запрашивать согласие на каждое собственное клиентское (настольное или мобильное приложение) обращение к авторизации. Идентификация собственного клиента по своей природе небезопасна, что противоречит конфиденциальному удостоверению клиентского приложения. Конфиденциальные клиентские приложения обмениваются секретом с платформой Microsoft Identity, чтобы доказать их подлинность. Платформа Microsoft Identity решила устранить эту небезопасность для потребительских услуг, запрашивая согласие пользователя при каждой авторизации приложения.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
