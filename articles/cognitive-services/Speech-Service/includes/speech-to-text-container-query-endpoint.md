---
title: Запрос конечной точки контейнера речи в текст
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2d96385f2d2d34d161739c55228220cf28871048
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132621"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Преобразование речи в текст или Пользовательское распознавание речи в текст

Контейнер предоставляет интерфейсы API конечной точки запроса на основе WebSocket, доступ к которым осуществляется через [пакет SDK для распознавания речи](../index.md). По умолчанию в пакете SDK для речевых функций используются службы речевого перевода. Чтобы использовать контейнер, вам необходимо изменить метод инициализации.

> [!TIP]
> При использовании речевого пакета SDK с контейнерами вам не нужно указывать ключ подписки на ресурсы службы распознавания речи Azure [или токен носителя проверки подлинности](../rest-speech-to-text.md#authentication).

Ознакомьтесь с указанными ниже примерами.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Смените этот вызов инициализации облака Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

для этого вызова с помощью [узла](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)контейнера:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

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
