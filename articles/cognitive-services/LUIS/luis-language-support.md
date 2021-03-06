---
title: Поддержка языков — LUIS
titleSuffix: Azure Cognitive Services
description: Служба LUIS имеет широкий набор функций. Не все компоненты имеют одинаковую языковую функциональную совместимость. Убедитесь, что функции, которые вас интересуют, поддерживаются для используемого языка и региональных параметров. Приложение LUIS учитывает конкретный язык и региональные параметры, которые нельзя изменять после установки.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: f6b95f76af4c83459ac81ff1703d8588f649326c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970548"
---
# <a name="language-and-region-support-for-luis"></a>Поддержка языков и регионов в LUIS

Служба LUIS имеет широкий набор функций. Не все компоненты имеют одинаковую языковую функциональную совместимость. Убедитесь, что функции, которые вас интересуют, поддерживаются для используемого языка и региональных параметров. Приложение LUIS учитывает конкретный язык и региональные параметры, которые нельзя изменять после установки.

## <a name="multi-language-luis-apps"></a>Многоязыковые приложения LUIS

Если вам требуется многоязыковое клиентское приложение LUIS, такое как чат-бот, у вас есть несколько вариантов. Если LUIS поддерживает все языки, можно разработать приложение LUIS для каждого языка. Каждое приложение LUIS получает уникальный идентификатор приложения и журнал конечной точки. Чтобы обеспечить распознавание речи для языка, не поддерживаемого LUIS, можно использовать [API Microsoft Translator](../Translator/translator-info-overview.md) для перевода фрагментов речи на поддерживаемый язык, отправки фрагментов в конечную точку LUIS и получения оценок.

## <a name="languages-supported"></a>Поддерживаемые языки

Служба LUIS распознает фрагменты речи на следующих языках:

| Язык |Язык  |  Предварительно созданная предметная область | Предварительно созданная сущность | Рекомендации по списку фраз | **[Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Тональность и<br>ключевые слова)|
|--|--|:--:|:--:|:--:|:--:|
| Английский (США) |`en-US` | ✔ | ✔  |✔|✔|
| Арабский (Предварительная версия — современные стандартные арабские) |`ar-AR`|-|-|-|-|
| *[Китайский](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Нидерландский |`nl-NL` |✔|  -   |-|✔|
| французский (Франция) |`fr-FR` |✔| ✔ |✔ |✔|
| французский (Канада) |`fr-CA` |-|   -   |-|✔|
| Немецкий |`de-DE` |✔| ✔ |✔ |✔|
| Хинди | `hi-IN`|-|-|-|-|
| Итальянский |`it-IT` |✔| ✔ |✔|✔|
| *[Японский](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Только ключевая фраза|
| Корейский |`ko-KR` |✔|   -   |-|Только ключевая фраза|
| Португальский (Бразилия) |`pt-BR` |✔| ✔ |✔ |не все вложенные языки и региональные параметры|
| испанский (Испания) |`es-ES` |✔| ✔ |✔|✔|
| Испанский (Мексика)|`es-MX` |-|  -   |✔|✔|
| Турецкий | `tr-TR` |✔|-|-|Только тональность|

Языковая поддержка зависит от [готовых сущностей](luis-reference-prebuilt-entities.md) и [готовых предметных областей](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>\* Примечания о поддержке японского языка

 - Поскольку служба LUIS не обеспечивает синтаксический анализ и не может понять разницу между почтительной и неформальной речью, необходимо включить различные уровни формальности в качестве примеров обучения для ваших приложений.
     - でございます не равно です.
     - です не равно だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Поддерживаемые языки API речи
Сведения о языках режима диктовки службы речи см. в разделе [Поддерживаемые языки](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) для службы "Речь".

### <a name="bing-spell-check-supported-languages"></a>Поддерживаемые языки проверки орфографии Bing
Список поддерживаемых языков и состояний см. в разделе [Поддерживаемые языки](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) проверки орфографии Bing.

## <a name="rare-or-foreign-words-in-an-application"></a>Редкие или иностранные слова в приложении
В языке и региональных параметрах `en-us` служба LUIS обучается для определения большинства английских слов, включая сленг. В языке и региональных параметрах `zh-cn` служба LUIS обучается для определения большинства символов китайского языка. Если при использовании редких слов в `en-us` или символов в `zh-cn` вы понимаете, что служба LUIS не может распознать это слово или символ, их можно добавить в [список фраз](luis-how-to-add-features.md). Например, слова за пределами языка и региональных параметров приложения, то есть иностранные слова, следует добавить в свойство "Список фраз".

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Гибридные языки
Гибридные языки объединяют слова из двух культур, например английского и китайского языка. Эти языки не поддерживаются в LUIS, так как приложение основано на одном языке.

## <a name="tokenization"></a>Выделение лексем
В целях машинного обучения LUIS разбивает фрагменты речи на [маркеры](luis-glossary.md#token), в зависимости от языка и региональных параметров.

|Язык|  каждый пробел и специальный символ | уровень символа|составные слова|[возвращаемая размеченная сущность](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Арабский|||||
|Китайский||✔||✔|
|Нидерландский|||✔|✔|
|Английский (en-us)|✔ ||||
|Французский (fr-FR)|✔||||
|Французский (fr-CA)|✔||||
|Немецкий|||✔|✔|
| Хинди |✔|-|-|-|-|
|Итальянский|✔||||
|Японский||||✔|
|Корейский||✔||✔|
|Португальский (Бразилия)|✔||||
|Испанский (es-ES)|✔||||
|Испанский (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Пользовательские версии анализатора маркеров

Следующие языки и региональные параметры имеют пользовательские версии маркеров:

|Язык и региональные параметры|Версия|Цель|
|--|--|--|
|Немецкий<br>`de-de`|1.0.0|Разделяет слова, разделив их с помощью токена на основе машинного обучения, который пытается разбить составные слова на отдельные компоненты.<br>Если пользователь вводит `Ich fahre einen krankenwagen` как utterance, он включается `Ich fahre einen kranken wagen`. Разрешение маркировки `kranken` и `wagen` независимо от разных сущностей.|
|Немецкий<br>`de-de`|1.0.2|Разделяет слова, разбивая их на пробелы.<br> Если пользователь вводит `Ich fahre einen krankenwagen` как utterance, он остается единственным маркером. Таким образом `krankenwagen` помечается как единая сущность. |

### <a name="migrating-between-tokenizer-versions"></a>Миграция между версиями маркеров
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID.

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

Разметка происходит на уровне приложения. Разметки на уровне версии не поддерживаются.

[Импортируйте файл как новое приложение](luis-how-to-start-new-app.md), а не версию. Это действие означает, что новое приложение имеет другой идентификатор приложения, но использует версию анализатора, указанную в файле.
