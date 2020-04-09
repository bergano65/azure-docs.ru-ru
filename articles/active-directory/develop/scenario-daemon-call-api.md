---
title: Позвоните в веб-API из приложения daemon - платформа идентификации Microsoft Azure
description: Узнайте, как создать приложение daemon, которое вызывает веб-AIS
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e440628526dada7655cc71f63fc9fff006cc5ef5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885452"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Приложение Daemon, которое вызывает веб-API - вызов веб-API из приложения

приложения .NET daemon могут вызывать веб-API. приложения .NET daemon также могут вызывать несколько предварительно утвержденных web-aIS.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Вызов веб-API из приложения daemon

Вот как использовать маркер для вызова API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>Вызов нескольких AIS

Для приложений daemon веб-аПО, которые вы называете, должны быть предварительно утверждены. Там нет постепенного согласия с приложениями daemon. (Нет никакого взаимодействия с пользователем.) Админ-арендатор должен заранее предоставить согласие на применение и все разрешения API. Если вы хотите позвонить несколько AI, вам нужно приобрести `AcquireTokenForClient`токен для каждого ресурса, каждый раз вызывая . MSAL будет использовать кэш маркеров приложения, чтобы избежать ненужных вызовов службы.

## <a name="next-steps"></a>Дальнейшие действия

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Приложение Daemon - переход к производству](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Приложение Daemon - переход к производству](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Приложение Daemon - переход к производству](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
