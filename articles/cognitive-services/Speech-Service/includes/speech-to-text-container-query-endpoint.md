---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: cba789b6a40dd23309bb94289b187209893908e3
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92116720"
---
Контейнер предоставляет интерфейсы API конечной точки запроса на основе WebSocket, доступ к которым осуществляется через [пакет SDK для распознавания речи](../index.yml). По умолчанию в пакете SDK для речевых функций используются службы речевого перевода. Чтобы использовать контейнер, вам необходимо изменить метод инициализации.

> [!TIP]
> При использовании речевого пакета SDK с контейнерами вам не нужно указывать ключ подписки на ресурсы службы распознавания речи Azure [или токен носителя проверки подлинности](../rest-speech-to-text.md#authentication).

См. следующие примеры.

# <a name="c"></a>[C#](#tab/csharp)

Смените этот вызов инициализации облака Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Чтобы использовать этот вызов с [узлом](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)контейнера, выполните следующие действия.

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

Для использования этого вызова с [конечной точкой](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)контейнера:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
