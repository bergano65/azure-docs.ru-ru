---
title: Как отслеживание использования памяти с использованием речевого пакета SDK — служба речи
titleSuffix: Azure Cognitive Services
description: Пакет SDK для речевых служб поддерживает множество языков программирования для преобразования речи в текст и перевода текста в речь, а также преобразования речи. В этой статье рассматривается встроенное в пакет SDK средство управления памятью.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456435"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Как отслеживание использования памяти пакетом SDK для речи

Речевой пакет SDK основан на базе машинного кода, который проецируется на несколько языков программирования с помощью ряда уровней взаимодействия. Каждая проекция, относящаяся к определенному языку, имеет идиоматикалли правильные функции для управления жизненным циклом объектов. Кроме того, пакет SDK для распознавания речи включает средства управления памятью для мониторинга использования ресурсов с помощью ведения журнала объектов и ограничений объектов. 

## <a name="how-to-read-object-logs"></a>Чтение журналов объектов

Если [включено ведение журнала для речевого пакета](how-to-use-logging.md), создаются теги отслеживания для включения наблюдения за историческими объектами. Эти теги включают: 

* `TrackHandle` либо `StopTracking` 
* Тип object
* Текущее количество объектов, отслеживаемых типом объекта, и текущего отслеживаемого числа.

Ниже приведен пример журнала. 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Установка порога предупреждения

Вы можете создать порог предупреждения и, если это пороговое значение превышено (при условии, что включено ведение журнала), в журнал записывается предупреждающее сообщение. Предупреждающее сообщение содержит дамп всех объектов в существовании вместе с их количеством. Эти сведения можно использовать для лучшего понимания проблем. 

Чтобы включить пороговое значение предупреждения, оно должно быть указано для объекта `SpeechConfig`. Этот объект проверяется при создании нового распознавателя. В следующих примерах Предположим, что вы создали экземпляр `SpeechConfig` с именем `config`:

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
> Значение этого свойства по умолчанию равно 10 000.

## <a name="set-an-error-threshold"></a>Установка порога ошибки 

С помощью речевого пакета SDK можно задать максимальное количество объектов, допустимых в определенный момент времени. Если этот параметр включен, то при достижении максимального числа попытки создать новые объекты распознавателя завершатся ошибкой. Существующие объекты будут продолжать работать.

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

Чтобы включить пороговое значение ошибки, оно должно быть указано для объекта `SpeechConfig`. Этот объект проверяется при создании нового распознавателя. В следующих примерах Предположим, что вы создали экземпляр `SpeechConfig` с именем `config`:

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
> Значение этого свойства по умолчанию — это максимальное значение, зависящее от платформы, для `size_t` типа данных. Обычное распознавание будет использовать от 7 до 10 внутренних объектов.

## <a name="next-steps"></a>Дальнейшие действия

* [Получите пробную подписку на службу распознавания речи](get-started.md)
* [Узнайте, как распознать речь с помощью микрофона](quickstarts/speech-to-text-from-microphone.md)