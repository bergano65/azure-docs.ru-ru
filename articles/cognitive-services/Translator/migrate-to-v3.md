---
title: Переход на API перевода текстов версии 3
titlesuffix: Azure Cognitive Services
description: Узнайте, как перейти с API перевода текстов версии 2 на версию 3.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: 2f0b2984bf2390a9af0b824495b84c71d04aeac2
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852849"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Переход с API перевода текстов версии 2 на версию 3

> [!NOTE]
> Версия 2 не рекомендуется к использованию начиная с 30 апреля 2018 г., а ее поддержка будет прекращена 30 апреля 2019 г.

Команда разработчиков Microsoft Translator выпустила API перевода текстов версии 3. Этот выпуск включает новые возможности и новый формат отправки данных в службу Microsoft Translator и получения их из нее. Кроме того, в этом выпуске исключены некоторые устаревшие методы. В этом документе содержатся сведения об изменении конфигурации приложений с целью реализации поддержки версии 3. 

В конце этой статьи содержатся полезные ссылки на дополнительные источники информации.

## <a name="summary-of-features"></a>Краткие сведения о функциях

* Отсутствие трассировки. В версии 3 функция "Без трассировки" применяется ко всем ценовым категориям на портале Azure. Это означает, что корпорация Майкрософт не будет сохранять текст, переданный в API версии 3.
* JSON. ФорматXML заменили на JSON. Все данные, отправляемые службе и получаемые от нее, находятся в формате JSON.
* Несколько целевых языков в одном запросе. Метод Translate работает с несколькими языками перевода в одном запросе. Например, один запрос может быть с английского на русский, а второй — с испанского на японский (или любая другая группа языков).
* Двуязычный словарь. В этот API-интерфейс добавлен двуязычный метод Dictionary. Этот метод имеет два вида: Dictionary lookup и Dictionary examples.
* В этот API-интерфейс добавлен метод Transliterate. Этот метод преобразует слова и предложения в одном скрипте (например, арабском) в другой скрипт (например, латинский).
* Языки. Новый метод Languages предоставляет сведения о языке в формате JSON. Его можно использовать с методами Translate, Dictionary и Transliterate.
* Новые возможности метода Translate. В метод Translate добавлены новые возможности, которые обеспечивают поддержку некоторых функций, которые использовались в API версии 2 как отдельные методы. В качестве примера можно привести метод TranslateArray.
* Метод Speak. API Microsoft Translator больше не поддерживает функцию преобразования текста в речь. Функция преобразования текста в речь доступна в [службе распознавания речи (Майкрософт)](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

Ниже приведен список методов версий 2 и 3, в котором указаны методы версии 3 и API-интерфейсы, предоставляющие возможности версии 2.

| Метод API версии 2   | Совместимость API версии 3 |
|:----------- |:-------------|
| Translate     | [Translate](reference/v3-0-translate.md)          |
| TranslateArray      | [Translate](reference/v3-0-translate.md)        |
| GetLanguageNames      | [Языки](reference/v3-0-languages.md)         |
| GetLanguagesForTranslate     | [Языки](reference/v3-0-languages.md)       |
| GetLanguagesForSpeak      | [Служба распознавания речи (Майкрософт)](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| Speak     | [Служба распознавания речи (Майкрософт)](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| Обнаружение     | [Detect](reference/v3-0-detect.md)         |
| DetectArray     | [Detect](reference/v3-0-detect.md)         |
| AddTranslation     | [API Microsoft Translator Hub](https://hub.microsofttranslator.com/Help/Download/Microsoft%20Translator%20Hub%20API%20Guide.pdf)         |
| AddTranslationArray    | [API Microsoft Translator Hub](https://hub.microsofttranslator.com/Help/Download/Microsoft%20Translator%20Hub%20API%20Guide.pdf)          |
| BreakSentences      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| GetTranslations      | Компонент больше не поддерживается.         |
| GetTranslationsArray      | Компонент больше не поддерживается.         |

## <a name="move-to-json-format"></a>Переход на формат JSON

Интерфейс API перевода текстов Microsoft Translator версии 2 принимал и возвращал данные в формате XML. В версии 3 все отправленные и полученные через API данные имеют формат JSON. Данные в формате XML больше не принимаются и не возвращаются в версии 3.

Это изменение может повлиять на некоторые аспекты приложения, написанного для API перевода текстов версии 2. Например, интерфейс API языков возвращает сведения о языке перевода текста, транслитерацию и два метода Dictionary. Вы можете запросить все сведения о языке для всех методов в одном вызове или по отдельности.

Метод Languages не требует проверки подлинности. Чтобы просмотреть все сведения о языке для версии 3 в формате JSON, перейдите по следующей ссылке:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Ключ проверки подлинности

В версии 3 поддерживается ключ проверки подлинности, используемый в версии 2. Новую подписку получать не нужно. В течение года переноса вы сможете использовать в своих программах как версию 2, так и версию 3. Это упростит выпуск новых версий при переходе с версии V2-XML на версию V3-JSON.

## <a name="pricing-model"></a>Модель ценообразования

Microsoft Translator V3 имеет такую же модель ценообразования, что и версия 2 (за символ, в том числе пробелы). Новые возможности в версии 3 внесли некоторые изменения в подсчет символов.

| Метод версии 3   | Символы, учитываемые при выставлении счетов |
|:----------- |:-------------|
| Языки     | Если символы не отправляются, плата не взимается.          |
| Translate     | Подсчет выполняется на основе отправляемого количества символов для перевода и количества языков перевода. Если оправляется 50 символов на 5 языках, получается 250 символов (50 x 5).           |
| Transliterate     | Количество символов, переданных для транслитерации, подсчитывается.         |
| Dictionary lookup и Dictionary example     | Количество символов, отправленных для поиска по словарю и в примерах, подсчитывается.         |
| BreakSentence     | Плата не взимается.       |
| Обнаружение     | Плата не взимается.      |

## <a name="v3-end-points"></a>Конечные точки версии 3

Глобальные

* api.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>Методы перевода текста API версии 3

[Языки](reference/v3-0-languages.md)

[Translate](reference/v3-0-translate.md)

[Transliterate](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Detect](reference/v3-0-detect.md)

[Dictionary/lookup](reference/v3-0-dictionary-lookup.md)

[Dictionary/example](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>Настройка

По умолчанию служба Microsoft Translator версии 3 использует технологию нейронного машинного перевода. Поэтому она не может использоваться центром Microsoft Translator Hub. Translator Hub поддерживает только устаревший статистический машинный перевод. Функцию перевода с использованием нейронных сетей можно настроить с помощью пользовательского переводчика. [Узнайте больше о настройке нейронного машинного перевода](customization.md)

Нейронный перевод с помощью API перевода текста версии 3 не поддерживает стандартные категории (SMT, speech, text, generalnn).


## <a name="links"></a>Ссылки

* [Политика конфиденциальности Майкрософт](https://privacy.microsoft.com/privacystatement)
* [Юридическая информация о платформе Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Условия служб Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [API перевода текстов версии 3.0](reference/v3-0-reference.md)
