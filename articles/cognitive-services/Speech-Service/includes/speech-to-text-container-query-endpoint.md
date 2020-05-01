---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: e5f38da6a18e8a4c59bea77f4ddd5a68db1e1902
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610754"
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

---
