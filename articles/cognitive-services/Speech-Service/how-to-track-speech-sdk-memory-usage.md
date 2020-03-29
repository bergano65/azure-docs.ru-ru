---
title: Как отслеживать использование памяти речи SDK - Служба речи
titleSuffix: Azure Cognitive Services
description: Служба речевого сообщения SDK поддерживает многочисленные языки программирования для преобразования речи в текст и текст к речи, наряду с переводом речи. В этой статье рассматриваются инструменты управления памятью, встроенные в SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456435"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Как отслеживать использование памяти речи SDK

Speech SDK основан на исходной базе кода, которая проецируется на несколько языков программирования через ряд уровней совместимости. Каждая языковая проекция имеет идиоматично правильные функции для управления жизненным циклом объекта. Кроме того, Speech SDK включает в себя инструмент управления памятью для отслеживания использования ресурсов с помощью регистрации объектов и ограничений объектов. 

## <a name="how-to-read-object-logs"></a>Как читать журналы объектов

Если [запись speech SDK включена,](how-to-use-logging.md)теги отслеживания исключаются для обеспечения наблюдения за историческими объектами. Эти теги включают в себя: 

* `TrackHandle` либо `StopTracking` 
* Тип object
* Текущее число объектов, отслеживаемых по типу объекта, и текущее число отслеживается.

Вот пример журнала: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Установить порог предупреждения

У вас есть возможность создать порог предупреждения, и если этот порог превышен (при условии включения регистрации) регистрируется предупреждающее сообщение. Предупреждающее сообщение содержит свалку всех существующих объектов вместе с их количеством. Эта информация может быть использована для лучшего понимания проблем. 

Для включения порога предупреждения он должен `SpeechConfig` быть указан на объекте. Этот объект проверяется при создании нового распознавателього. В следующих примерах предположим, что вы создали `SpeechConfig` `config`экземпляр называется:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> Значение по умолчанию для этого свойства составляет 10 000.

## <a name="set-an-error-threshold"></a>Установить порог ошибки 

Используя Speech SDK, можно установить максимальное количество объектов, разрешенных в данный момент времени. Если эта настройка включена, при ударе максимального числа попытки создания новых объектов распознавания завершают неудачу. Существующие объекты будут продолжать работать.

Вот пример ошибки:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Для включения порога ошибки он `SpeechConfig` должен быть указан на объекте. Этот объект проверяется при создании нового распознавателього. В следующих примерах предположим, что вы создали `SpeechConfig` `config`экземпляр называется:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> Значение по умолчанию для этого свойства — `size_t` это максимальное значение для конкретного платформы для типа данных. Типичное распознавание будет потреблять от 7 до 10 внутренних объектов.

## <a name="next-steps"></a>Дальнейшие действия

* [Получите пробную подписку на услугу Speech](get-started.md)
* [Узнайте, как распознать речь с помощью микрофона](quickstarts/speech-to-text-from-microphone.md)