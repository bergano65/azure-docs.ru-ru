---
title: Ссылки на документацию по обогащению данных с помощью ИИ
titleSuffix: Azure Cognitive Search
description: Аннотированный перечень статей, учебников, примеров и записей блога, связанных с рабочими нагрузками обогащения данных с помощью ИИ в службе "Когнитивный поиск Azure".
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: cf0332909e053a0875d41a00d15f196b193ba15e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466717"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Ресурсы документации для обогащения данных с помощью ИИ в Когнитивном поиске Azure

Обогащение данных с помощью ИИ — это информационная функция индексирования в службе "Когнитивный поиск Azure", которая находит скрытые сведения в нетекстовых источниках и неразделенном тексте, преобразуя их в текстовое содержимое с полными возможностями поиска в службе "Когнитивный поиск Azure".

Далее представлена полная документация со статьями по обогащению данных с помощью ИИ.

## <a name="getting-started"></a>Начало работы
+ [Общие сведения об ИИ в Когнитивном поиске Azure](cognitive-search-concept-intro.md)
+ [Краткое руководство Создание когнитивного набора навыков на портале Azure](cognitive-search-quickstart-blob.md)
+ [Руководство. Обогащенное индексирование с помощью ИИ](cognitive-search-tutorial-blob.md)
+ [Пример. Создание пользовательского навыка для обогащения ИИ](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Практические руководства
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [How to reference annotations in a skillset](cognitive-search-concept-annotations-syntax.md) (Создание ссылок на аннотации в наборе навыков когнитивного поиска)
+ [Сопоставление обогащенных полей с индексом, поддерживающим поиск](cognitive-search-output-field-mapping.md)
+ [How to process and extract information from images in cognitive search scenarios](cognitive-search-concept-image-scenarios.md) (Обработка и извлечение информации из изображений в сценариях когнитивного поиска)
+ [Как перестроить индекс службы "Когнитивный поиск Azure"](search-howto-reindex.md)
+ [How to add a custom skill to a cognitive search pipeline](cognitive-search-custom-skill-interface.md) (Добавление пользовательского навыка в конвейер когнитивного поиска)
+ [Советы по устранению неполадок](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Справочные материалы

+ [Встроенные навыки](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Пользовательские навыки
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Устаревшие навыки](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Создание набора навыков (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Создание индексатора (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>См. также

+ [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/) (Руководство по REST Службы поиска Azure)
+ [Indexers in Azure Cognitive Search](search-indexer-overview.md) (Индексаторы в службе "Когнитивный поиск Azure")
+ [Что собой представляет Когнитивный поиск Azure?](search-what-is-azure-search.md)
