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
ms.openlocfilehash: 338b638d6b33bcbbb5cf377643a96c71b0d314bd
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775197"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Управляющее приложение, вызывающее веб-API — вызов веб-API из приложения

Приложения управляющей программы .NET могут вызывать веб-API. Приложения управляющей программы .NET также могут вызывать несколько предварительно утвержденных веб-API.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Вызов веб-API из управляющего приложения

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

Для приложений управляющей программы необходимо предварительно утвердить веб-API, которые вызываются. Нет добавочного согласия с приложениями управляющей программы. (Нет взаимодействия с пользователем.) Администратор клиента должен заранее предоставить согласие на доступ к приложению и всем разрешениям API. Если требуется вызвать несколько API, необходимо получить маркер для каждого ресурса, каждый раз вызывая `AcquireTokenForClient`. MSAL будет использовать кэш маркеров приложений, чтобы избежать ненужных вызовов служб.

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
