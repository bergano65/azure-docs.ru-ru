---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1773e22a54cc86e7736c91e4be757caa0250cbf7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422305"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Речь к тексту или пользовательские речи к тексту

Контейнер предоставляет apIs-конечные точки запроса на основе websocket, которые доступны через [Speech SDK.](../index.yml) По умолчанию Speech SDK использует онлайн-сервисы речи. Чтобы использовать контейнер, вам необходимо изменить метод инициализации.

> [!TIP]
> При использовании Speech SDK с контейнерами не требуется предоставлять ключ подписки ресурса Azure Speech [или маркер носителя аутентификации.](../rest-speech-to-text.md#authentication)

Ознакомьтесь с указанными ниже примерами.

# <a name="c"></a>[C#](#tab/csharp)

Смените этот вызов инициализации облака Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

к этому вызову с помощью [контейнерного хоста:](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

Смените этот вызов инициализации облака Azure:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

к этому вызову с помощью [контейнерного хоста:](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
