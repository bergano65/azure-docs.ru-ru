---
title: Вызов веб-API из управляющего приложения — платформа Microsoft Identity | Службы
description: Узнайте, как создать управляющее приложение, вызывающее веб-API
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: 46f1676ab1bdcf2b23907824bb9bf543b5f28ce6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962616"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Управляющее приложение, вызывающее веб-API — вызов веб-API из приложения

Управляющее приложение может вызывать веб-API из приложения управляющей программы .NET или вызывать несколько предварительно утвержденных веб-API.

## <a name="calling-a-web-api-daemon-application"></a>Вызов приложения управляющей программы веб-API

Вот как можно использовать маркер для вызова API:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

## <a name="calling-several-apis"></a>Вызов нескольких API

Для приложений управляющей программы необходимо предварительно утвердить веб-API, которые вызываются. С приложениями управляющей программы не будет выполняться добавочное согласие (нет взаимодействия с пользователем). Администратор клиента должен предварительно согласиться с приложением и всеми разрешениями API. Если требуется вызвать несколько API, необходимо получить маркер для каждого ресурса, каждый раз вызывая `AcquireTokenForClient`. MSAL будет использовать кэш маркеров приложений, чтобы избежать ненужных вызовов служб.

## <a name="next-steps"></a>Дальнейшие действия

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Приложение управляющей программы — переместить в рабочую среду](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Приложение управляющей программы — переместить в рабочую среду](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Приложение управляющей программы — переместить в рабочую среду](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
