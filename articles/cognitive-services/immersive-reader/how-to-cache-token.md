---
title: Кэширование маркера проверки подлинности
titleSuffix: Azure Cognitive Services
description: В этой статье показано, как кэшировать маркер проверки подлинности.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946169"
---
# <a name="how-to-cache-the-authentication-token"></a>Кэширование маркера проверки подлинности

В этой статье показано, как кэшировать маркер проверки подлинности для повышения производительности приложения.

## <a name="using-aspnet"></a>Использование ASP.NET

Импортируйте пакет NuGet **Microsoft. IdentityModel. Clients. ActiveDirectory** , который используется для получения маркера. Затем используйте следующий код, чтобы получить `AuthenticationResult`с использованием значений проверки подлинности, полученных при [создании иммерсивного ресурса чтения](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

Объект `AuthenticationResult` имеет свойство `AccessToken`, которое является фактическим маркером, который будет использоваться при запуске иммерсивное средство чтения с помощью пакета SDK. Он также имеет свойство `ExpiresOn`, которое обозначает, когда истечет срок действия маркера. Перед запуском иммерсивное средство чтения можно проверить, истек ли срок действия маркера, и получить новый токен, только если срок его действия истек.

## <a name="using-nodejs"></a>Использование Node. JS

Добавьте пакет [**request**](https://www.npmjs.com/package/request) NPM в проект. Используйте следующий код, чтобы получить маркер, используя значения проверки подлинности, полученные при [создании иммерсивного ресурса чтения](./how-to-create-immersive-reader.md).

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

Свойство `expires_on` — это дата и время истечения срока действия маркера, выраженное в виде количества секунд с 1 января 1970 UTC. Используйте это значение, чтобы определить, истек ли срок действия токена, прежде чем пытаться получить новый.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Изучите [Справочник по пакету SDK для иммерсивное средство чтения](./reference.md)