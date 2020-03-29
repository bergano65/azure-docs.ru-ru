---
title: Кэширование маркера проверки подлинности
titleSuffix: Azure Cognitive Services
description: В этой статье будет показан способ кэширования маркера проверки подлинности.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946169"
---
# <a name="how-to-cache-the-authentication-token"></a>Как кэшировать токен проверки подлинности

В этой статье показано, как кэшировать токен проверки подлинности, чтобы повысить производительность приложения.

## <a name="using-aspnet"></a>Использование ASP.NET

Импортируйте пакет **Microsoft.IdentityModel.Customers.ActiveDirectory** NuGet, который используется для приобретения токена. Затем используйте следующий код `AuthenticationResult`для приобретения, используя значения аутентификации, которые вы получили при [создании ресурса Immersive Reader.](./how-to-create-immersive-reader.md)

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

Объект `AuthenticationResult` имеет `AccessToken` свойство, которое является фактическим маркером, который вы будете использовать при запуске Immersive Reader с помощью SDK. Он также `ExpiresOn` имеет свойство, которое обозначает, когда срок действия токена истечет. Перед запуском Immersive Reader вы можете проверить, истек ли срок действия токена, и приобрести новый токен только в том случае, если он истек.

## <a name="using-nodejs"></a>Использование Node.JS

Добавьте пакет [**запроса**](https://www.npmjs.com/package/request) npm в проект. Используйте следующий код для приобретения маркера, используя значения аутентификации, которые вы получили при [создании ресурса Immersive Reader.](./how-to-create-immersive-reader.md)

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

Свойство `expires_on` является датой и временем, в которое истекает срок истечения токена, выраженным как количество секунд с 1 января 1970 UTC. Используйте это значение, чтобы определить, истек ли срок действия токена перед попыткой приобрести новый.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со справочной документацией по [пакету SDK для иммерсивного средства чтения](./reference.md).