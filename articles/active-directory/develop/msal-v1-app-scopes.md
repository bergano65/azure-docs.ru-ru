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
ms.date: 04/23/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0590614e1c1bc7331246e76fa26a6567a05324e6
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532341"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Области для веб-API, которые принимают маркеры версии 1.0

Приложение для веб-API (ресурс) Azure AD для разработчиков (версии 1.0) предоставляет клиентским приложениям разрешения OAuth2. Эти области действия разрешений могут быть назначены клиентским приложениям во время предоставления согласия. Дополнительные сведения об `oauth2Permissions` см. в [справке по манифестам приложений Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Области для запроса доступа к конкретным разрешениям OAuth2 в приложении версии 1.0
Если вы хотите получать маркеры для конкретной области приложения версии 1.0 (например, для Azure AD Graph, то есть https:\//graph.windows.net), вам необходимо создать области путем объединения идентификатора требуемого ресурса с нужным разрешением OAuth2 для этого ресурса.

Например, для доступа от имени пользователя веб-API версии 1.0 с URI идентификатора приложения `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Если вам нужны права на чтение и запись для MSAL.NET Azure Active Directory через API Azure AD Graph (https:\//graph.windows.net/), следует создать список областей, как в следующем фрагменте кода:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Если вам нужны права на запись в область, соответствующую API Azure Resource Manager (https:\//management.core.windows.net/), вам следует запросить следующую область (обратите внимание на две косые черты).

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Здесь необходимо использовать две косые черты, поскольку API Azure Resource Manager требует наличия косой черты в утверждении "aud", а вторая косая черта отделяет имя API от области действия.

Azure AD использует следующую логику:

- Для конечной точки ADAL (версия 1.0) с маркером доступа версии 1.0 (единственный поддерживаемый вариант) aud=resource.
- Для MSAL (конечная точка платформы удостоверений Майкрософт (версии 2.0)), которая запрашивает маркер доступа для ресурса, принимающего маркеры версии 2.0, aud=resource.AppId
- Для конечной точки MSAL (версии 2.0), которая запрашивает маркер доступа для ресурса, принимающего маркеры версии 1.0 (как в примере выше), AAD выполняет синтаксический анализ параметра audience из запрошенной области, используя все символы до последней косой черты в качестве идентификатора ресурса. Поэтому если https:\//database.windows.net ожидает для audience значение "https:\//database.windows.net/", следует запросить область https:\//database.windows.net//.default. См. также на GitHub вопрос [№ 747: об отсутствии завершающей косой черты в URL-адресе ресурса, что приводит к сбою аутентификации SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

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

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Области запроса потоковой/управляющей программы клиентских учетных данных
Если используется клиентский поток учетных данных, следует передать область `/.default`. Для AAD это означает "все разрешения на уровне приложения, которые администратор согласился предоставлять при регистрации приложения".
