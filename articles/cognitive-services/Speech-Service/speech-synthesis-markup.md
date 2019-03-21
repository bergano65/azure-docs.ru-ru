---
title: Речи синтеза Markup Language (SSML) - службы распознавания речи
titleSuffix: Azure Cognitive Services
description: С помощью языка Speech Synthesis Markup Language можно управлять произношением и интонацией при преобразовании текста в речь.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 57fc7e699d88dbe777750e3acdb7f96794b66fc0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837178"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Язык разметки синтеза речи (SSML)

Speech Synthesis Markup Language (SSML) — это язык разметки на базе XML, который позволяет контролировать произношение и *интонацию* при преобразовании текста в речь. (Интонацией называют ритм и тон речи — ее музыкальное звучание, если можно так выразиться.) С его помощью слова можно обозначать фонетически, добавлять указания по интерпретации чисел, вставлять паузы, контролировать тон, громкость, скорость речи и многое другое. Дополнительные сведения см. на странице, посвященной [Speech Synthesis Markup Language (SSML) версии 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/).

Полный список поддерживаемых языков, языковых стандартов и голосовых моделей (нейронных и стандартных) см. в разделе [языковой поддержки](language-support.md#text-to-speech).

В следующих разделах представлены примеры распространенных задач синтеза речи.

## <a name="add-a-break"></a>Добавление паузы
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Изменение скорости речи

Скорость озвучивания могут применяться к стандартной голоса в word или уровне предложений. В то время как скорость озвучивания может применяться только к нейронной голоса на уровне предложений.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Произношение
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Изменение громкости

Изменения тома могут применяться к стандартной голоса в word или уровне предложений. Тогда как изменения тома может применяться только к нейронной голоса на уровне предложений.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Изменение тона

Шаг изменения могут применяться к стандартной голоса в word или уровне предложений. В то время как шаг изменения могут применяться только к нейронной голоса на уровне предложений.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Изменение контура тона

> [!IMPORTANT]
> Контурная изменения тона с нейронной голоса не поддерживаются.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Использование нескольких голосов
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Good morning!
</voice>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Дальнейшие действия

* [Поддержка языков и регионов в API службы "Речь"](language-support.md)
