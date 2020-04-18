---
title: Встроенная обработка текста и изображения во время индексации
titleSuffix: Azure Cognitive Search
description: Извлечение данных, естественный язык, когнитивные навыки обработки изображений добавляют семантику и структуру к необработанму содержимому в конвейере Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b1cf1750924ab8ea8afff6ac788683565433866b
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618017"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Встроенные когнитивные навыки обработки текста и изображения во время индексации (Azure Cognitive Search)

В этой статье вы узнаете о когнитивных навыках, предоставляемых Azure Cognitive Search, которые можно включить в набор навыков для извлечения содержимого и структуры. *Когнитивный навык* — это модуль или операция, которая каким-то образом преобразует содержимое. Часто этот компонент извлекает данные или предполагает структуру, расширяя доступное понимание входных данных. Почти всегда выходные данные основаны на тексте. *Набор навыков* — это совокупность навыков, определяющих конвейер обогащения. 

> [!NOTE]
> По мере расширения сферы охвата за счет увеличения частоты обработки, добавления дополнительных документов или добавления большего числа алгоритмов ИИ необходимо [прикрепить ресурс Cognitive Services.](cognitive-search-attach-cognitive-services.md) Плата взимается при вызове API в Cognitive Services и извлечении изображений при распознавании документов в службе "Когнитивный поиск Azure". За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Плата за извлечение изображений указана на [странице с ценами на службу "Когнитивный поиск Azure"](https://go.microsoft.com/fwlink/?linkid=2042400).
>
> Функция [постепенного обогащения (предварительного просмотра)](cognitive-search-incremental-indexing-conceptual.md) позволяет предоставить кэш, который позволяет индексатору быть более эффективным при управлении только когнитивными навыками, которые необходимы, если вы измените свой набор навыков в будущем, экономя ваше время и деньги.


## <a name="built-in-skills"></a>Встроенные навыки

Некоторые навыки позволяют гибко регулировать свои входные и выходные данные. Как правило, большинство навыков основано на предварительно обученных моделях, поэтому вы не можете обучить модель, используя собственные обучающие данные. Приведенная ниже таблица перечисляет и описывает навыки, предоставляемые корпорацией Майкрософт. 

| Навык | Описание |
|-------|-------------|
|[Microsoft.Skills.Text.CustomEntityLookUpSkill](cognitive-search-skill-custom-entity-lookup.md)| Ищет текст из пользовательского, определенного пользователем списка слов и фраз.|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Этот навык использует предварительно обученную модель для выявления важных фраз в зависимости от расположения терминов, лингвистических правил, близости к другим терминам и того, насколько необычным является термин в исходных данных. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Этот навык использует предварительно обученную модель, чтобы определить используемый язык (один идентификатор языка на документ). Если в одних сегментах текста используется несколько языков, выводится код основного используемого языка.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Объединяет текст из коллекции полей в одном поле.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Этот навык использует предварительно обученную модель, чтобы установить сущности для фиксированного набора категорий: пользователей, расположений, организаций, электронных адресов, URL-адресов, полей даты и времени. |
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Этот навык использует заранее подготовленную модель для извлечения персональной информации из данного текста. Навык также дает различные варианты маскировки обнаруженных объектов PII в тексте.  |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Этот навык использует предварительно обученную модель, чтобы оценить положительную или отрицательную тональность отдельных записей. Оценка находится в пределах от 0 до 1. Нейтральные оценки применяются в неопределенных случаях, когда установить тональность не удается, а также для текста, считающегося нейтральным.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Разбивает текст на страницы, чтобы можно было постепенно обогатить или дополнить содержимое. |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | Этот навык использует заранее подготовленную модель для перевода вхотворного текста на различные языки для нормализации или локализации случаев использования. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Этот навык использует алгоритм обнаружения изображений, чтобы определить содержимое изображения и создать текстовое описание. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Оптическое распознавание символов. |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | Позволяет фильтровать, назначать значение по умолчанию и объединять данные на основе условия.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Извлекает содержимое из файла в конвейере обогащения. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Сопоставляет выходные данные со сложным типом (составной тип данных, который может использоваться для полного имени, многострочного адреса или сочетания фамилии и личного идентификатора). |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Позволяет расширять конвейер обогащения ИИ, делая вызов HTTP в пользовательский Web API |


Для руководства по созданию [пользовательского навыка,](cognitive-search-custom-skill-web-api.md)см. [Как определить пользовательский интерфейс](cognitive-search-custom-skill-interface.md) и [Пример: Создание пользовательского навыка для обогащения ИИ](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>См. также раздел

+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [Определение интерфейса пользовательских навыков](cognitive-search-custom-skill-interface.md)
+ [Учебник: Обогащенная индексация с помощью ИИ](cognitive-search-tutorial-blob.md)
