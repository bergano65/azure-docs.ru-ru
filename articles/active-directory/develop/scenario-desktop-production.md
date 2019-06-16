---
title: Классическое приложение, что вызовы, веб-API (перемещение в рабочую среду) - платформой Microsoft identity
description: Научитесь создавать приложения для настольных систем, вызовы веб-API-интерфейсы (перемещение в рабочую среду)
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
ms.openlocfilehash: 2343a416bd810792e7267b94395f953aa4f880a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111186"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Классическое приложение, вызывающее веб-интерфейсы API — переносу в рабочую среду

Эта статья содержит сведения, чтобы улучшить приложение дальнейшей и переместите его в рабочую среду.

## <a name="handling-errors-in-desktop-applications"></a>Обработка ошибок в настольных приложениях

В разных потоках вы узнали, как обрабатывать ошибки для автоматической потоков (как показано во фрагментах кода). Вы уже также знаете что существуют случаи, когда требуется (добавочное согласие и условного доступа).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Способы получения согласия пользователя заранее для нескольких ресурсов

> [!NOTE]
> Получение согласия для нескольких ресурсов работает для платформы удостоверений Microsoft, но не для Azure Active Directory (Azure AD) B2C. Azure AD B2C поддерживает только согласия администратора, не согласия пользователя.

Конечная точка платформы (версии 2.0) Microsoft identity не позволяет получить маркер для нескольких ресурсов за один раз. Таким образом `scopes` может содержать только областей для одного ресурса. Убедитесь, что предварительно согласия пользователя для нескольких ресурсов с помощью `extraScopesToConsent` параметра.

К примеру при наличии двух ресурсов, которые имеют два области каждого:

- `https://mytenant.onmicrosoft.com/customerapi` — с помощью 2 областей `customer.read` и `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` — с помощью 2 областей `vendor.read` и `vendor.write`

Следует использовать `.WithAdditionalPromptToConsent` модификатор, который имеет `extraScopesToConsent` параметра.

Например:

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

Этот вызов можно получить маркер доступа для первого веб-API.

Если необходимо вызвать второй веб-API, можно вызвать:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Личная учетная запись Майкрософт требует reconsenting при каждом запуске приложения

Для Microsoft личных учетных записей пользователей reprompting на согласие при каждом вызове собственного клиента (для настольных и мобильных приложений) для авторизации является запланированным поведением. Собственный клиент удостоверений по своей природе небезопасен (в отличие от конфиденциального клиентского приложения, который exchange секрет с платформой Microsoft Identity, чтобы подтвердить свою личность). Платформы удостоверений Microsoft выбрал запрашивая у пользователя согласие, каждый раз, когда приложение авторизовано, чтобы устранить это отсутствие безопасности для пользовательских служб.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
