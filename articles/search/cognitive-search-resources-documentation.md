---
title: Ресурсы документации по когнитивному поиску (служба "Поиск Azure")
description: Аннотированный перечень статей, руководств, примеров и записей блога, связанных с рабочими нагрузками когнитивного поиска в службе "Поиск Azure".
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 3a23828d98b14c094aa751d3fb2e53575c5e6479
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347834"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Ресурсы документации для рабочих нагрузок когнитивного поиска

Вошедший в общедоступный Поиск — это новый слой обогащения в службе индексирования поиска Azure, который находит скрытые сведения в нетекстовых источниках и неотличающемся тексте, преобразуя ее в полнотекстовый Поисковый контент в службе поиска Azure.

Далее представлена полная документация со статьями по когнитивному поиску.

## <a name="getting-started"></a>Приступая к работе
+ [What is cognitive search?](cognitive-search-concept-intro.md) (Что такое когнитивный поиск?)
+ [Краткое руководство Создание конвейера когнитивного поиска с использованием навыков и примера данных](cognitive-search-quickstart-blob.md)
+ [Руководство. Вызов API когнитивного поиска (предварительная версия)](cognitive-search-tutorial-blob.md)
+ [Пример. Создание пользовательского навыка для Когнитивного поиска](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Практические руководства
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [How to reference annotations in a skillset](cognitive-search-concept-annotations-syntax.md) (Создание ссылок на аннотации в наборе навыков когнитивного поиска)
+ [Сопоставление обогащенных полей с индексом, поддерживающим поиск](cognitive-search-output-field-mapping.md)
+ [How to process and extract information from images in cognitive search scenarios](cognitive-search-concept-image-scenarios.md) (Обработка и извлечение информации из изображений в сценариях когнитивного поиска)
+ [How to rebuild an Azure Search index](search-howto-reindex.md) (Как перестроить индекс Поиска Azure)
+ [How to add a custom skill to a cognitive search pipeline](cognitive-search-custom-skill-interface.md) (Добавление пользовательского навыка в конвейер когнитивного поиска)
+ [Советы по устранению неполадок](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Ссылка

+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
  + [Microsoft. Skills. Text. Кэйфрасикстрактионскилл](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. Skills. Text. Транслатионскилл (Предварительная версия)](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. Skills. util. Кондитионалскилл](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Пользовательские навыки
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Устаревшие навыки](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Создание набора навыков (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Создание индексатора (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>См. также

+ [REST API службы "Поиск Azure"](https://docs.microsoft.com/rest/api/searchservice/)
+ [Индексаторы в службе поиска Azure](search-indexer-overview.md)
+ [Что такое поиск Azure?](search-what-is-azure-search.md)
