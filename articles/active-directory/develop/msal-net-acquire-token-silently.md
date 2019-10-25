---
title: Получение маркера в автоматическом режиме (Библиотека проверки подлинности Майкрософт для .NET)
titleSuffix: Microsoft identity platform
description: Узнайте, как получить маркер доступа автоматически (из кэша маркеров) с помощью библиотеки проверки подлинности Microsoft для .NET (MSAL.NET).
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
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dc573c37aab95006f647509080fb9883d8650c7
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802943"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Получение маркера из кэша маркеров с помощью MSAL.NET

При получении маркера доступа с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET) маркер кэшируется. Когда приложению требуется токен, сначала необходимо вызвать метод `AcquireTokenSilent`, чтобы проверить, находится ли допустимый маркер в кэше. Во многих случаях можно получить другой токен с дополнительными областями на основе маркера в кэше. Можно также обновить маркер, когда он поступает в начало срока действия (так как кэш маркеров также содержит маркер обновления).

Рекомендуемый шаблон — сначала вызвать метод `AcquireTokenSilent`.  Если `AcquireTokenSilent` не удается, получите маркер с помощью других методов.

В следующем примере приложение сначала пытается получить маркер из кэша маркеров.  Если создается исключение `MsalUiRequiredException`, приложение получает маркер в интерактивном режиме. 

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
