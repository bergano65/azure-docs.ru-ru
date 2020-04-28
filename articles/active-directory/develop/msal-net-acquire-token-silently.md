---
title: Получение маркера из кэша (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Узнайте, как получить маркер доступа автоматически (из кэша маркеров) с помощью библиотеки проверки подлинности Microsoft для .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77084840"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Получение маркера из кэша маркеров с помощью MSAL.NET

При получении маркера доступа с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET) маркер кэшируется. Когда приложению требуется токен, сначала необходимо вызвать `AcquireTokenSilent` метод, чтобы проверить, находится ли допустимый маркер в кэше. Во многих случаях можно получить другой токен с дополнительными областями на основе маркера в кэше. Можно также обновить маркер, когда он поступает в начало срока действия (так как кэш маркеров также содержит маркер обновления).

Рекомендуемый шаблон — сначала вызвать `AcquireTokenSilent` метод.  Если `AcquireTokenSilent` происходит сбой, получите маркер с помощью других методов.

В следующем примере приложение сначала пытается получить маркер из кэша маркеров.  При возникновении `MsalUiRequiredException` исключения приложение получает маркер в интерактивном режиме. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
