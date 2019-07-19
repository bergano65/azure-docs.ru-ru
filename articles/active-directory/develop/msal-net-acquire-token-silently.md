---
title: Получение маркера в автоматическом режиме (Библиотека проверки подлинности Майкрософт для .NET) | Службы
description: Узнайте, как получить маркер доступа автоматически (из кэша маркеров) с помощью библиотеки проверки подлинности Microsoft для .NET (MSAL.NET).
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
ms.date: 07/16/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 309e912f2adf5249770b40a631ed62f7cb3113e5
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277942"
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
