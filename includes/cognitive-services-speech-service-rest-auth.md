---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 9cad860b8808dd2682995768c282d8376ab5d9be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480540"
---
## <a name="authentication"></a>Authentication

Каждый запрос требует заголовок авторизации. В этой таблице показано, какие заголовки поддерживаются для каждой службы:

| Поддерживаемые заголовки авторизации | Преобразование речи в текст. | Преобразование текста в речь |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Yes | Нет  |
| Авторизация: Носитель | Yes | Yes |

При использовании заголовка `Ocp-Apim-Subscription-Key` необходимо предоставить только ключ подписки. Например: 

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

При использовании заголовка `Authorization: Bearer` необходимо выполнять запрос к конечной точке `issueToken`. В этом запросе ключ подписки обменивается на маркер доступа, действительный в течение 10 минут. В следующих разделах вы узнаете, как получать, использовать и обновлять маркер.


### <a name="how-to-get-an-access-token"></a>Как получить маркер доступа

Чтобы получить маркер доступа, необходимо отправить запрос в конечную точку `issueToken`, используя заголовок `Ocp-Apim-Subscription-Key` и ключ подписки.

Поддерживаются следующие регионы и конечные точки:

[!INCLUDE [](./cognitive-services-speech-service-endpoints-token-service.md)]

Используйте эти примеры, чтобы создать запрос на получение маркера доступа.

#### <a name="http-sample"></a>Пример HTTP

Это простой HTTP-запрос для получения маркера. Замените `YOUR_SUBSCRIPTION_KEY` своим ключом подписки на службу распознавания речи. Если ваша подписка не находится в регионе "Западная часть США", замените заголовок `Host` на имя узла в вашем регионе.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Тело ответа содержит маркер доступа в формате JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Пример для PowerShell

Это простой сценарий PowerShell для получения маркера доступа. Замените `YOUR_SUBSCRIPTION_KEY` своим ключом подписки на службу распознавания речи. Обязательно используйте правильную конечную точку для региона, который соответствует вашей подписке. В этом примере используется конечная точка для западной части США.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>Пример cURL

cURL — это программа командной строки, доступная в Linux (и в подсистеме Windows для Linux). Эта команда cURL показывает, как получить маркер доступа. Замените `YOUR_SUBSCRIPTION_KEY` своим ключом подписки на службу распознавания речи. Обязательно используйте правильную конечную точку для региона, который соответствует вашей подписке. В этом примере используется конечная точка для западной части США.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Пример на языке C#

Этот класс C# показывает, как получить маркер доступа. Передайте ключ подписки на службу распознавания речи при создании экземпляра класса. Если регион вашей подписки — не западная часть США, измените значение `FetchTokenUri` в соответствии с регионом своей подписки.

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Пример на языке Python

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'

def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Как использовать маркер доступа

Маркер доступа должен отправляться в службу в виде заголовка `Authorization: Bearer <TOKEN>`. Каждый маркер доступа действителен в течение 10 минут. Вы можете получить новый маркер в любое время, но чтобы уменьшить сетевой трафик и задержку, мы рекомендуем использовать один маркер в течение девяти минут.

Ниже приведен пример HTTP-запроса REST API преобразования текста в речь.

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
