---
title: Классическое приложение, вызывающее веб-API (переместить в рабочую среду) — платформа Microsoft Identity
description: Узнайте, как создать классическое приложение, вызывающее веб-API (переместить в рабочую среду).
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
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a353b4577f8cfa9ba279ad2793e1a7ab8b27e55
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268338"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Классическое приложение, вызывающее веб-API — переход в рабочую среду

Эта статья содержит подробные сведения о более подробном улучшении приложения и его перемещении в рабочую среду.

## <a name="handling-errors-in-desktop-applications"></a>Обработка ошибок в приложениях для настольных систем

В разных потоках вы узнали, как обрабатывались ошибки для безошибочных потоков (как показано в фрагментах кода). Вы также видели случаи, когда требуется взаимодействие (последовательное согласие и условный доступ).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Как заранее получить согласие пользователя на несколько ресурсов

> [!NOTE]
> Получение согласия для нескольких ресурсов работает для платформы Microsoft Identity, но не для Azure Active Directory (Azure AD) B2C. Azure AD B2C поддерживает только разрешение администратора, а не согласие пользователя.

Конечная точка платформы Microsoft Identity Platform (v 2.0) не позволяет получить маркер для нескольких ресурсов одновременно. `scopes` Поэтому параметр может содержать только области для одного ресурса. Вы можете убедиться, что пользователь заранее предоставил несколько ресурсов с помощью `extraScopesToConsent` параметра.

Например, если у вас есть два ресурса, каждая из которых имеет две области:

- `https://mytenant.onmicrosoft.com/customerapi`— с 2 областями `customer.read` и`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`— с 2 областями `vendor.read` и`vendor.write`

Следует использовать `.WithAdditionalPromptToConsent` модификатор, который `extraScopesToConsent` имеет параметр.

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

SWIFT

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

## <a name="next-steps"></a>Следующие шаги

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
