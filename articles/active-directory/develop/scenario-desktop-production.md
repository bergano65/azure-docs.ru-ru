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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a5e57d0ef37a3a2e758e42f122c8e014c94958
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919989"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Классическое приложение, вызывающее веб-API — переход в рабочую среду

В этой статье содержатся сведения о перемещении классического приложения, вызывающего веб-API, в рабочую среду.

## <a name="handling-errors-in-desktop-applications"></a>Обработка ошибок в приложениях для настольных систем

В разных потоках вы узнали, как обрабатывались ошибки для безошибочных потоков (как показано в фрагментах кода). Вы также видели случаи, когда требуется взаимодействие (последовательное согласие и условный доступ).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Как заранее получить согласие пользователя на несколько ресурсов

> [!NOTE]
> Получение согласия для нескольких ресурсов работает для платформы Microsoft Identity, но не для Azure Active Directory (Azure AD) B2C. Azure AD B2C поддерживает только разрешение администратора, а не согласие пользователя.

Конечная точка платформы Microsoft Identity Platform (v 2.0) не позволяет получить маркер для нескольких ресурсов одновременно. Таким образом, параметр `scopes` может содержать только области для одного ресурса. Вы можете убедиться, что пользователь заранее предоставил несколько ресурсов с помощью параметра `extraScopesToConsent`.

Например, если у вас есть два ресурса, каждая из которых имеет две области:

- `https://mytenant.onmicrosoft.com/customerapi` с 2 областями `customer.read` и `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` с 2 областями `vendor.read` и `vendor.write`

Следует использовать модификатор `.WithAdditionalPromptToConsent` с параметром `extraScopesToConsent`.

Например:

### <a name="in-msalnet"></a>В MSAL.NET

```CSharp
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

Этот вызов получит маркер доступа для первого веб-API.

При необходимости вызова второго веб-API можно вызвать `AcquireTokenSilent` API:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>При каждом запуске приложения личная учетная запись Майкрософт требует досогласия пользователя

Для пользователей личных учетных записей Майкрософт необходимо повторно запросить согласие для каждого собственного клиента (настольного или мобильного приложения), чтобы авторизовать. Идентификация собственного клиента по своей природе небезопасна (что противоречит конфиденциальному клиентскому приложению, которое обменивается данными с платформой идентификации Майкрософт для подтверждения своей личности). Платформа Microsoft Identity решила снизить этот уровень безопасности для потребительских услуг, запрашивая согласие пользователя, каждый раз при авторизации приложения.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
