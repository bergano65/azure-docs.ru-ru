---
title: Области для приложений v1.0 (MSAL) Azure
description: Дополнительные сведения об областях разрешений для приложения версии 1.0, использующего библиотеку проверки подлинности Майкрософт (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 61d07c1ba912a0e24b2f4e5fa67243b4525db367
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536188"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Области для веб-API, принимающий токены v1.0

Разрешения OAuth2 — это области разрешений, которые приложение Azure Active Directory (Azure AD) для разработчиков (v1.0) приложение Web API (ресурс) предоставляет клиентским приложениям. Эти области действия разрешений могут быть назначены клиентским приложениям во время предоставления согласия. Дополнительные сведения об `oauth2Permissions` см. в [справке по манифестам приложений Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Области для запроса доступа к конкретным разрешениям OAuth2 в приложении версии 1.0

Приобрести токены для определенных областей приложения v1.0 (например, https://graph.microsoft.com)Microsoft Graph API, который является, создать области, совышая желаемый идентификатор ресурса с желаемым разрешением OAuth2 для этого ресурса.

Например, для доступа от имени пользователя веб-API версии 1.0 с URI идентификатора приложения `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Чтобы читать и писать с помощью MSAL.NET Azure AD\/с помощью API Microsoft Graph (https: /graph.microsoft.com/), необходимо создать список областей, показанных в следующих примерах:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Чтобы написать область, соответствующую API-менеджеру\/ресурсов Azure (https:/management.core.windows.net/), необходимо запросить следующую область (обратите внимание на два слэша):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Здесь необходимо использовать две косые черты, поскольку API Azure Resource Manager требует наличия косой черты в утверждении "aud", а вторая косая черта отделяет имя API от области действия.

Azure AD использует следующую логику:

- Для ADAL (Azure AD v1.0) конечная точка с токеном доступа v1.0 (единственно возможно), aud'resource
- Для MSAL (платформа идентификации Microsoft (v2.0)) запросить токен доступа для ресурса, принимающего токены v2.0,`aud=resource.AppId`
- Для MSAL (конечная точка v2.0) запрашивает токен доступа для ресурса, который принимает токен доступа v1.0 (что имеет место выше), Azure AD разбирает нужную аудиторию из запрашиваемой области, забрав все до последней слэши и используя его в качестве идентификатора ресурсов. Поэтому, если https:\//database.windows.net ожидает аудиторию\/"https: /database.windows.net/", вам нужно будет\/запросить область "https: /database.windows.net//.default". Смотрите также GitHub вопрос [#747: Ресурс URL в задней черта опущена, что вызвало sql Auth сбоя](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Области для запроса доступа ко всем разрешениям в приложении версии 1.0

Если вы хотите получить маркер для всех статических областей приложения версии 1.0, добавьте ".default" к URI идентификатора приложения API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Области запроса приложения потока/daemon клиента

Если используется клиентский поток учетных данных, следует передать область `/.default`. Для AAD это означает "все разрешения на уровне приложения, которые администратор согласился предоставлять при регистрации приложения".
