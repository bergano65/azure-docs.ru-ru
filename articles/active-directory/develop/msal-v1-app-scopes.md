---
title: Область разрешений для приложения версии 1.0 (библиотека проверки подлинности Майкрософт) | Azure
description: Дополнительные сведения об областях разрешений для приложения версии 1.0, использующего библиотеку проверки подлинности Майкрософт (MSAL).
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
ms.date: 11/25/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70a8a5859c7f1e2353b53d01a25a0ca39e0b04dd
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532975"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Области для веб-API, которые принимают маркеры версии 1.0

Разрешения OAuth2 — это области разрешений, которые предоставляют клиентским приложениям Azure Active Directory (Azure AD) для разработчиков (версия 1.0). Эти области действия разрешений могут быть назначены клиентским приложениям во время предоставления согласия. Дополнительные сведения об `oauth2Permissions` см. в [справке по манифестам приложений Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Области для запроса доступа к конкретным разрешениям OAuth2 в приложении версии 1.0

Чтобы получить токены для конкретных областей приложения версии 1.0 (например, Azure AD Graph, который является https:\//graph.windows.net), необходимо создать области путем сцепления требуемого идентификатора ресурса с нужным разрешением OAuth2 для этого ресурса.

Например, для доступа от имени пользователя веб-API версии 1.0 с URI идентификатора приложения `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Для чтения и записи в MSAL.NET Azure AD с помощью Azure AD API Graph (HTTPS:\//graph.windows.net/) необходимо создать список областей, как показано в следующих примерах:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Чтобы записать область, соответствующую Azure Resource Manager API (HTTPS:\//management.core.windows.net/), необходимо запросить следующую область (Обратите внимание на две косые черты):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Здесь необходимо использовать две косые черты, поскольку API Azure Resource Manager требует наличия косой черты в утверждении "aud", а вторая косая черта отделяет имя API от области действия.

Azure AD использует следующую логику:

- Для конечной точки ADAL (Azure AD v 1.0) с маркером доступа v 1.0 (только возможный), AUD = Resource.
- Для конечной точки MSAL (Microsoft Identity Platform (v 2.0)), запрашивающей маркер доступа для ресурса, принимающего токены версии 2.0, `aud=resource.AppId`
- Для MSAL (конечная точка версии 2.0) с запросом маркера доступа для ресурса, который принимает маркер доступа v 1.0 (этот вариант описан выше), Azure AD анализирует нужную аудиторию из запрошенной области, принимая все до последней косой черты и используя ее в качестве идентификатора ресурса. Таким образом, если HTTPS:\//database.windows.net требуется аудитория "https:\//database.windows.net/", необходимо запросить область "https:\//database.windows.net//.default". См. также вопрос GitHub [#747: Конечная косая черта URL-адреса ресурса пропущена, что привело к сбою проверки подлинности SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

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

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Области для запроса приложения потока учетных данных клиента или управляющей программы

Если используется клиентский поток учетных данных, следует передать область `/.default`. Для AAD это означает "все разрешения на уровне приложения, которые администратор согласился предоставлять при регистрации приложения".
