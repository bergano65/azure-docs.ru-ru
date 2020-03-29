---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/21/2020
ms.author: dapine
ms.openlocfilehash: 9b4317064196c4ea3d761fd1a0bd43a764054fe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77563316"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Речь к тексту или пользовательские речи к тексту

Контейнер предоставляет apIs-конечные точки запроса на основе websocket, которые доступны через [Speech SDK.](../index.yml) По умолчанию Speech SDK использует онлайн-сервисы речи. Чтобы использовать контейнер, вам необходимо изменить метод инициализации.

> [!TIP]
> При использовании Speech SDK с контейнерами не требуется предоставлять ключ подписки ресурса Azure Speech [или маркер носителя аутентификации.](../rest-speech-to-text.md#authentication)

Ознакомьтесь с указанными ниже примерами.

# <a name="c"></a>[C #](#tab/csharp)

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
