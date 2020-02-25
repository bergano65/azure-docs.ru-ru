---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/21/2020
ms.author: dapine
ms.openlocfilehash: 9b4317064196c4ea3d761fd1a0bd43a764054fe6
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77563316"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Преобразование речи в текст или Пользовательское распознавание речи в текст

Контейнер предоставляет интерфейсы API конечной точки запроса на основе WebSocket, доступ к которым осуществляется через [пакет SDK для распознавания речи](../index.yml). По умолчанию в пакете SDK для речевых функций используются службы речевого перевода. Чтобы использовать контейнер, вам необходимо изменить метод инициализации.

> [!TIP]
> При использовании речевого пакета SDK с контейнерами вам не нужно указывать ключ подписки на ресурсы службы распознавания речи Azure [или токен носителя проверки подлинности](../rest-speech-to-text.md#authentication).

Ознакомьтесь с указанными ниже примерами.

# <a name="c"></a>[C#](#tab/csharp)

Смените этот вызов инициализации облака Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

для этого вызова с помощью [узла](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)контейнера:

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

для этого вызова с помощью [узла](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)контейнера:

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
