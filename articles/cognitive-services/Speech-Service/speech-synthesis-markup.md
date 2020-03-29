---
title: Язык речевого синтеза разметки (SSML) - Речевая служба
titleSuffix: Azure Cognitive Services
description: С помощью языка Speech Synthesis Markup Language можно управлять произношением и интонацией при преобразовании текста в речь.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 7d5dd79399b15ade90173a55aeb71dacbc61fa78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80365806"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Улучшение синтеза с языком синтеза речи (SSML)

Язык разметки синтеза речи (SSML) — это язык разметки на основе XML, который позволяет разработчикам указывать, как текст ввода преобразуется в синтезированную речь с помощью службы «текст-речь». По сравнению с простым текстом, SSML позволяет разработчикам точно настроить шаг, произношение, скорость речи, объем и больше текста к речи вывода. Нормальные знаки препинания, такие как приостановка после периода или использование правильной интонации, когда предложение заканчивается вопросительным знаком, автоматически обрабатываются.

Реализация службы речи SSML основана на версии языка синтеза речи World Wide Web Consortium [1.0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Китайские, японские и корейские иероглифы считаются двумя символами для выставления счетов. Дополнительные сведения см. на странице [цен](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Стандартные, нейронные и пользовательские голоса

Выберите из стандартных и нейронных голосов, или создать свой собственный пользовательский голос, уникальный для вашего продукта или бренда. Стандартные голоса 75 'доступны на более чем 45 языках и языках, а 5 нейронных голосов доступны на четырех языках и языках. Полный список поддерживаемых языков, языковых стандартов и голосовых моделей (нейронных и стандартных) см. в разделе [языковой поддержки](language-support.md).

Чтобы узнать больше о стандартных, нейронных и пользовательских голоса, см [Текст к речи обзор](text-to-speech.md).

## <a name="special-characters"></a>Специальные символы

При использовании SSML, имейте в виду, что специальные символы, такие как кавычки, апострофы и скобки должны быть избеудены. Для получения дополнительной информации [см. Расширяемый язык разметки (XML) 1.0: Приложение D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Поддерживаемые элементы SSML

Каждый документ SSML создается с элементами SSML (или тегами). Эти элементы используются для регулировки высоты, просодии, объема и т.д. В следующих разделах подробно описано, как используется каждый элемент, и когда элемент требуется или необязательный.  

> [!IMPORTANT]
> Не забудьте использовать двойные кавычки вокруг значений атрибутов. Стандарты для хорошо сформированных, действительных XML требуют, чтобы значения атрибутов были заключены в двойные кавычки. Например, `<prosody volume="90">` является хорошо сформированным, `<prosody volume=90>` действительным элементом, но это не так. SSML может не распознавать значения атрибутов, которые не находятся в котировках.

## <a name="create-an-ssml-document"></a>Создание документа SSML

`speak`является корневым элементом и **необходим** для всех документов SSML. Элемент `speak` содержит важную информацию, такую как версия, язык и определение словарного запаса разметки.

**Синтаксис**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Атрибуты**

| Атрибут | Описание | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `version` | Указывает версию спецификации SSML, используемую для интерпретации разметки документов. Текущая версия 1.0. | Обязательно |
| `xml:lang` | Определяет язык корневого документа. Значение может содержать нижний регистр, двухбуквенный `en`языковой код (например, ) или языковой код и верхнюю часть страны/региона (например, `en-US`). | Обязательно |
| `xmlns` | Украшив URI в документ, определяющий словарный запас разметки (типы элементов и имена атрибутов) документа SSML. Текущий URI http://www.w3.org/2001/10/synthesis. | Обязательно |

## <a name="choose-a-voice-for-text-to-speech"></a>Выберите голос для текста в речи

Элемент `voice` необходим. Он используется для указания голоса, который используется для текста к речи.

**Синтаксис**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Атрибуты**

| Атрибут | Описание | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `name` | Определяет голос, используемый для вывода текста в речь. Полный список поддерживаемых голосов можно узнать в [службе поддержки Language.](language-support.md#text-to-speech) | Обязательно |

**Пример**

> [!NOTE]
> В этом `en-US-AriaRUS` примере используется голос. Полный список поддерживаемых голосов можно узнать в [службе поддержки Language.](language-support.md#text-to-speech)

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Использование нескольких голосов

В `speak` элементе можно указать несколько голосов для вывода текста в речи. Эти голоса могут быть на разных языках. Для каждого голоса текст должен быть `voice` завернут в элемент. 

**Атрибуты**

| Атрибут | Описание | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `name` | Определяет голос, используемый для вывода текста в речь. Полный список поддерживаемых голосов можно узнать в [службе поддержки Language.](language-support.md#text-to-speech) | Обязательно |

> [!IMPORTANT]
> Несколько голосов несовместимы с функцией «граница слова». Функция «граница слова» должна быть отключена, чтобы использовать несколько голосов.

### <a name="disable-word-boundary"></a>Граница отсутсвия слова

В зависимости от языка речи SDK, `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` вы `false` установите свойство на экземпляр `SpeechConfig` объекта.

# <a name="c"></a>[C #](#tab/csharp)

Для получения дополнительной <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Для получения дополнительной <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Для получения дополнительной <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Для получения дополнительной <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Для получения дополнительной <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Для получения дополнительной <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Для получения дополнительной <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Пример**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Отрегулируйте стили речи

> [!IMPORTANT]
> Регулировка говоря стилей будет работать только с нейронными голосами.

По умолчанию служба «текст к речи» синтезирует текст, используя нейтральный стиль речи как для стандартных, так и для нейронных голосов. С нейронными голосами, вы можете настроить стиль речи, чтобы `<mstts:express-as>` выразить жизнерадостность, сочувствие, или чувства с элементом. Это дополнительный элемент, уникальный для службы speech.

В настоящее время, говоря стиль корректировки поддерживаются для этих нейронных голосов:
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`
* `pt-BR-FranciscaNeural`

Изменения применяются на уровне предложения, и стиль варьируется в зависимости от голоса. Если стиль не поддерживается, служба возвращает речь в нейтральном стиле speaking по умолчанию.

**Синтаксис**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**Атрибуты**

| Атрибут | Описание | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `style` | Определяет стиль речи. В настоящее время, говоря стили голос-специфически. | Требуется при настройке стиля речи для нейронного голоса. При `mstts:express-as`использовании, то стиль должен быть предоставлен. При предоставлении недействительного значения этот элемент будет проигнорирован. |

Используйте эту таблицу, чтобы определить, какие говорящие стили поддерживаются для каждого нервного голоса.

| Голосовая связь | Style | Описание |
|-------|------|-------------|
| `en-US-AriaNeural` | `style="newscast"` | Выражает формальный и профессиональный тон для повествования новостей |
| | `style="customerservice"` | Выражает дружелюбный и полезный тон для поддержки клиентов |
| | `style="chat"` | Выражает случайный и расслабленный тон |
| | `style="cheerful"` | Выражает позитивный и счастливый тон |
| | `style="empathetic"` | Выражает чувство заботы и понимания |
| `zh-CN-XiaoxiaoNeural` | `style="newscast"` | Выражает формальный и профессиональный тон для повествования новостей |
| | `style="customerservice"` | Выражает дружелюбный и полезный тон для поддержки клиентов |
| | `style="assistant"` | Выражает теплый и расслабленный тон для цифровых помощников  |
| | `style="lyrical"` | Выражает эмоции мелодичным и сентиментальным образом |
| `pt-BR-FranciscaNeural` | `style="cheerful"` | Выражает позитивный и счастливый тон |

**Пример**

Этот фрагмент SSML иллюстрирует, `<mstts:express-as>` как элемент используется для изменения `cheerful`стиля речи на .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Добавить или удалить перерыв/паузу

Используйте `break` элемент для вставки пауз (или перерывов) между словами или предотвращения автоматически добавленных пауз службой текста в речи.

> [!NOTE]
> Используйте этот элемент, чтобы переопределить поведение текста к речи (TTS) для слова или фразы, если синтезированная речь для этого слова или фразы звучит неестественно. Установить, `strength` чтобы `none` предотвратить просодический перерыв, который автоматически вставляется текст-к-речи службы.

**Синтаксис**

```xml
<break strength="string" />
<break time="string" />
```

**Атрибуты**

| Атрибут | Описание | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `strength` | Определяет относительную продолжительность паузы с использованием одного из следующих значений:<ul><li>none</li><li>x-слабый</li><li>Слабым</li><li>средний (по умолчанию)</li><li>надежный</li><li>x-сильный</li></ul> | Необязательный |
| `time` | Определяет абсолютную продолжительность паузы в секундах или миллисекундах. Примеры допустимых `2s` значений`500` | Необязательный |

| Силы | Описание |
|----------|-------------|
| Нет, или если значение не предусмотрено | 0 мс |
| x-слабый | 250 мс |
| Слабым | 500 мс |
| средняя | 750 мс |
| надежный | 1000 мс |
| x-сильный | 1250 мс |


**Пример**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Указать пункты и предложения

`p`и `s` элементы используются для обозначения соответственно абзацев и предложений. При отсутствии этих элементов служба «текст к речи» автоматически определяет структуру документа SSML.

Элемент `p` может содержать текст и `audio`следующие элементы: `mstts:express-as`, `s` `break`, `phoneme` `prosody`, `say-as` `sub`, , и .

Элемент `s` может содержать текст и `audio`следующие элементы: `break` `sub`, , `phoneme` `prosody`, `say-as` `mstts:express-as`, и .

**Синтаксис**

```XML
<p></p>
<s></s>
```

**Пример**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Используйте фонемы для улучшения произношения

Элемент `ph` используется для фонетического произношения в документах SSML. Элемент `ph` может содержать только текст, никаких других элементов. Всегда обеспечивайте читаемую человеком речь в качестве запасного.

Фонетические алфавиты состоят из телефонов, которые состоят из букв, цифр или символов, иногда в комбинации. Каждый телефон описывает уникальный звук речи. Это в отличие от латинского алфавита, где любая буква может представлять несколько произнесенных звуков. Рассмотрим различные произношения буквы "c" в словах "конфеты" и "прекратить", или различные произношения буквы комбинации "th" в словах "вещь" и "те".

**Синтаксис**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Атрибуты**

| Атрибут | Описание | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `alphabet` | Определяет фонетический алфавит для использования при синтезе произношения строки в `ph` атрибуте. Строка, определяющая алфавит, должна быть указана буквами нижнего регистра. Ниже приведены возможные алфавиты, которые вы можете указать.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Международный фонетический <span class="docon docon-navigate-external x-hidden-focus"></span> алфавит</a></li><li>`sapi`&ndash; [Телефонное алфавитное обслуживание речи](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; Универсальный набор телефонов</li></ul><br>Алфавит применяется только `phoneme` к элементу. | Необязательный |
| `ph` | Строка, содержащая телефоны, которые указывают `phoneme` произношение слова в элементе. Если указанная строка содержит непризнанные телефоны, служба «текст-к-речи» (TTS) отклоняет весь документ SSML и не производит ни одного вывода речи, указанного в документе. | Требуется при использовании фонем. |

**Примеры**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Используйте пользовательский лексикон для улучшения произношения

Иногда TTS не может точно произнести слово, например, название компании или иностранное. Разработчики могут определить чтение этих сущностей `phoneme` `sub` в SSML с помощью и теги, или `lexicon` определить чтение нескольких сущностей, ссылаясь на пользовательский файл лексикона с помощью тега.

**Синтаксис**

```XML
<lexicon uri="string"/>
```

**Атрибуты**

| Атрибут | Описание | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `uri` | Адрес внешнего документа PLS. | Обязательный элемент. |

**Использования**

Шаг 1: Определите пользовательский лексикон 

Чтение сущностей можно определить по списку пользовательских элементов лексикона, хранящихся в виде файла .xml или .pls.

**Пример**

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

Каждый `lexeme` элемент является элементом лексикона. `grapheme`содержит текст, описывающий ортограф `lexeme`. Форма считываемого средства может быть предоставлена как `alias`. Строка телефона может `phoneme` быть предоставлена элементом.

Элемент `lexicon` содержит по `lexeme` крайней мере один элемент. `lexeme` Каждый элемент содержит `grapheme` по крайней `grapheme` `alais`мере `phoneme` один элемент и один или несколько, и элементы. Элемент `grapheme` содержит текст, описывающий <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">офтизг. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a> Элементы `alias` используются для обозначения произношения аббревиатуры или сокращенного термина. Элемент `phoneme` предоставляет текст, `lexeme` описывающий, как произносится.

Для получения дополнительной информации о пользовательских файл лексикона, см [Произношение Лексикон спецификации (PLS) Версия 1.0](https://www.w3.org/TR/pronunciation-lexicon/) на веб-сайте W3C.

Шаг 2: Загрузить пользовательский файл лексикона, созданный в шаге 1 онлайн, вы можете хранить его в любом месте, и мы предлагаем вам хранить его в Microsoft Azure, [например, Azure Blob Storage.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

Шаг 3: Обратитесь к пользовательскому файлу лексикона в SSML

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"BtW" будет читаться как "Кстати". "Benigni" будет прочитан при условии, что IPA "бениньи".  

**Ограничение**
- Размер файла: пользовательский словарь файла максимальный предел размера 100KB, если за этот размер, запрос синтеза не удастся.
- Обновление кэша лексикона: пользовательский лексикон будет кэшироваться с URI в качестве ключа на TTS Service при первой загрузке. Лексикон с тем же URI не будет перезагружен в течение 15 минут, так что пользовательские изменения лексикона необходимо ждать не более 15 минут, чтобы ввести в силу.

**Телефоники службы речи**

В приведенной выше примере мы используем Международный фонетический алфавит, также известный как набор телефонов IPA. Мы предлагаем разработчикам использовать IPA, потому что это международный стандарт. Учитывая, что IPA не легко запомнить, служба речи определяет фонетический `de-DE`набор `es-ES` `ja-JP`для `zh-CN`семи `zh-TW`языков (,`en-US` `fr-FR`, , , и ).

Вы можете `sapi` использовать в качестве `alphabet` долина для атрибута с пользовательскими лексиконами, как показано ниже:

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Для получения дополнительной информации о подробной речи службы фонетический алфавит, [см.](speech-ssml-phonetic-sets.md)

## <a name="adjust-prosody"></a>Отрегулируйте просодию

Элемент `prosody` используется для определения изменений в шаге, контуре, диапазоне, скорости, продолжительности и объеме для вывода текста в речи. Элемент `prosody` может содержать текст и `audio`следующие элементы: `sub`, `s` `break`, `p` `phoneme`, `prosody` `say-as`, , и .

Поскольку значения просодических атрибутов могут варьироваться в широком диапазоне, распознавание речи интерпретирует назначенные значения как предложение о том, какими должны быть фактические просодические значения выбранного голоса. Служба текстовых сообщений ограничивает или заменяет значения, которые не поддерживаются. Примерами неподдерживаемых значений являются смола 1 МГц или объем 120.

**Синтаксис**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Атрибуты**

| Атрибут | Описание | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `pitch` | Указывает базовую высоту для текста. Вы можете выразить поле как:<ul><li>Абсолютное значение, выраженное в виде числа, за которым следует "Гц" (Герц). Например, 600 Гц.</li><li>Относительное значение, выраженное в виде числа, предшествуемого "Я" или "-" и за которым следует "Hz" или "st", которое определяет сумму для изменения поля. Например: 80 Гц или -2. "st" указывает на то, что блок изменения является полутоном, который составляет половину тона (половина шага) по стандартной диатонической шкале.</li><li>Постоянное значение:<ul><li>x-низкий</li><li>low</li><li>средняя</li><li>high</li><li>x-высокий</li><li>default</li></ul></li></ul>. | Необязательный |
| `contour` | Контур не поддерживается для нейронных голосов. Контур представляет изменения в поле. Эти изменения представлены как массив целей в определенных временных позициях на выходе речи. Каждая цель определяется наборами парабителей. Пример: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Первое значение в каждом наборе параметров определяет расположение изменения шага в процентах от продолжительности текста. Второе значение определяет сумму для повышения или понижения высоты поля, используя относительное `pitch`значение или значение перечисления для шага (см.). | Необязательный |
| `range` | Значение, представляющее диапазон шага для текста. Вы можете `range` выразить использование тех же абсолютных значений, относительных `pitch`значений или значений перечисления, используемых для описания. | Необязательный |
| `rate` | Указывает скорость разговора текста. Вы можете `rate` выразить как:<ul><li>Относительное значение, выраженное как число, которое выступает в качестве множителя по умолчанию. Например, значение *1* не приводит к изменению курса. Значение *0,5* приводит к снижению ставки вдвое. Значение *3* приводит к утроению курса.</li><li>Постоянное значение:<ul><li>x-медленный</li><li>медленный</li><li>средняя</li><li>быстрая</li><li>x-быстрый</li><li>default</li></ul></li></ul> | Необязательный |
| `duration` | Период времени, который должен истечения в то время как синтез речи (TTS) служба читает текст, в секундах или миллисекундах. Например, *2s* или *1800ms*. | Необязательный |
| `volume` | Указывает уровень громкости говорящего голоса. Вы можете выразить громкость как:<ul><li>Абсолютное значение, выраженное как число в диапазоне от 0,0 до 100,0, от *самого тихого* до *самого громкого*. Например, 75. По умолчанию 100.0.</li><li>Относительное значение, выраженное в виде числа, предшествуемого "Я" или "-", которое определяет сумму для изменения объема. Например, 10 евро или -5,5.</li><li>Постоянное значение:<ul><li>silent</li><li>x-мягкий</li><li>Мягкий</li><li>средняя</li><li>Громко</li><li>x-громкий</li><li>default</li></ul></li></ul> | Необязательный |

### <a name="change-speaking-rate"></a>Изменение скорости речи

Скорость говорящих может быть применена к стандартным голосам на уровне слова или предложения. В то время как скорость речи может быть применена только к нейронным голосам на уровне предложения.

**Пример**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Изменение громкости

Изменения объема могут быть применены к стандартным голосам на уровне слова или предложения. В то время как изменения громкости могут быть применены только к нейронным голосам на уровне предложения.

**Пример**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Изменение тона

Изменения шага могут быть применены к стандартным голосам на уровне слова или предложения. В то время как изменения шага могут быть применены только к нейронным голосам на уровне предложения.

**Пример**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Изменение контура тона

> [!IMPORTANT]
> Изменения контура шага не поддерживаются нейронными голосами.

**Пример**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>сказать, как элемент

`say-as`является необязательным элементом, указывающим тип содержимого (например, число или дату) текста элемента. Это дает рекомендации для речевого синтеза двигателя о том, как произносить текст.

**Синтаксис**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Атрибуты**

| Атрибут | Описание | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `interpret-as` | Отображается тип содержимого текста элемента. Список типов приведен ниже. | Обязательно |
| `format` | Предоставляет дополнительную информацию о точном форматировании текста элемента для типов содержимого, которые могут иметь неоднозначные форматы. SSML определяет форматы для типов контента, которые их используют (см. таблицу ниже). | Необязательный |
| `detail` | Указывает уровень детализации, который нужно произнесь. Например, этот атрибут может потребовать, чтобы двигатель синтеза речи произносил знаки препинания. Стандартные значения не определены для. `detail` | Необязательный |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Ниже приведены поддерживаемые типы содержимого для атрибутов `interpret-as` и `format` атрибутов. Включите `format` атрибут `interpret-as` только в том случае, если установлен на дату и время.

| интерпретировать-как | format | Интерпретация |
|--------------|--------|----------------|
| `address` | | Текст говорится как адрес. Двигатель синтеза речи произносит:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Как "Я на 150-м суде северо-востоке Редмонд Вашингтон". |
| `cardinal`, `number` | | Текст говорится как кардинальный номер. Двигатель синтеза речи произносит:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Как "Есть три альтернативы". |
| `characters`, `spell-out` | | Текст говорится как отдельные буквы (изложены). Двигатель синтеза речи произносит:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Как "T E S T." |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | Текст произнесена как дата. Атрибут `format` определяет формат даты *(день, мзмесяц и y'year).* Двигатель синтеза речи произносит:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Как "Сегодня девятнадцатое октября две тысячи шестнадцать". |
| `digits`, `number_digit` | | Текст говорится как последовательность отдельных цифр. Двигатель синтеза речи произносит:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Как "1 2 4 4 6 7 8 9." |
| `fraction` | | Текст говорится как дробное число. Двигатель синтеза речи произносит:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Как "три восьмых дюйма". |
| `ordinal` | | Текст говорится как ординальное число. Двигатель синтеза речи произносит:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Как "Выбрать третий вариант". |
| `telephone` | | Текст говорится как номер телефона. Атрибут `format` может содержать цифры, представляющие код страны. Например, "1" для Соединенных Штатов или "39" для Италии. Двигатель синтеза речи может использовать эту информацию для руководства своим произношением номера телефона. Номер телефона может также включать код страны, и если да, то `format`имеет приоритет над кодом страны в . Двигатель синтеза речи произносит:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Как "Мой номер код области восемь восемь восемь пять пять пять два два два". |
| `time` | hms12, hms24 | Текст говорится как время. Атрибут `format` определяет, указывается ли время с помощью 12-часовых часов (hms12) или 24-часовых часов (hms24). Используйте толстую кишку, чтобы отделить числа, представляющие часы, минуты и секунды. Приведены всякая примера времени: 12:35, 1:14:32, 08:15 и 02:50:45. Двигатель синтеза речи произносит:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Как "Поезд отправляется в четыре Утра". |

**Использования**

Элемент `say-as` может содержать только текст.

**Пример**

Двигатель синтеза речи говорит следующий пример как «Ваш первый запрос был для одной комнаты на 19-ое октября 20 10 с ранним прибытием на 20 30 pm.»
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Добавление записанного звука

`audio`является дополнительным элементом, который позволяет вставить mp3 аудио в документ SSML. Тело звукового элемента может содержать простой текст или разметку SSML, которая произнесена, если аудиофайл недоступен или невоспроизводим. Кроме того, `audio` элемент может содержать текст `audio`и `break` `p`следующие элементы: , `s`, `phoneme`, `prosody`, `say-as`, и `sub`.

Любой аудиозвук, включенный в документ SSML, должен соответствовать этим требованиям:

* MP3 должен быть размещен на доступной в Интернете конечная точка HTTPS. HTTPS необходим, и домен, на котором размещается файл MP3, должен представить действительный, надежный сертификат TLS/SSL.
* MP3 должен быть действительным файлом MP3 (MPEG v2).
* Скорость бита должна быть 48 кбит/с.
* Коэффициент выборки должен собираться 16 000 Гц.
* Общее время, общее время для всех текстовых и аудиофайлов за один ответ не может превышать девяносто (90) секунд.
* MP3 не должен содержать какую-либо конкретную клиентскую или другую конфиденциальную информацию.

**Синтаксис**

```xml
<audio src="string"/></audio>
```

**Атрибуты**

| Атрибут | Описание | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `src` | Укажите местоположение/URL аудиофайла. | Требуется при использовании аудиоэлемента в документе SSML. |

**Пример**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Добавление фонового аудио

Элемент `mstts:backgroundaudio` позволяет добавлять фоновый звук в документы SSML (или смешивать аудиофайл с текстом к речи). С `mstts:backgroundaudio` вы можете цикл аудио файл в фоновом режиме, исчезают в начале текста к речи, и исчезают в конце текста к речи.

Если фоновый звук короче, чем текст к речи или исчезают, он будет цикл. Если он длиннее текста к речи, он остановится, когда исчезнет.

В документе SSML допускается только один фоновый аудиофайл. Тем не менее, вы `audio` можете перемежать теги в элементе, `voice` чтобы добавить дополнительный звук в ваш документ SSML.

**Синтаксис**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Атрибуты**

| Атрибут | Описание | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `src` | Укажите местоположение/URL файла фонового аудио. | Требуется при использовании фонового аудио в документе SSML. |
| `volume` | Определяет объем фонового аудиофайла. **Принятые значения:** `0` к инклюзивному. `100` Значение по умолчанию — `1`. | Необязательный |
| `fadein` | Определяет продолжительность фонового звука "угасает" как миллисекунды. Значение по `0`умолчанию, что эквивалентно не исчезают дюйма **Принятые значения:** `0` к инклюзивному. `10000`  | Необязательный |
| `fadeout` | Определяет продолжительность фонового звука за тридцать секунд. Значение по `0`умолчанию, что эквивалентно не исчезают. **Принятые значения:** `0` к инклюзивному. `10000`  | Необязательный |

**Пример**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Дальнейшие действия

* [Поддержка языков и регионов в API службы "Речь"](language-support.md)
