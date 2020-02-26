---
title: Новые возможности API анализа текста
titleSuffix: Text Analytics - Azure Cognitive Services
description: В этой статье содержатся сведения о новых выпусках и функциях Анализ текста Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188800"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Новые возможности в API "Анализ текста"

API анализа текста обновляется на постоянной основе. В этой статье содержатся сведения о новых выпусках и функциях, чтобы оставаться в курсе последних нововведений.

## <a name="february-2020"></a>Февраль 2020 г.

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Поддержка пакета SDK для общедоступной предварительной версии API анализа текста v3

В рамках [единой версии пакета SDK для Azure](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)пакет sdk для API анализа текста v3 теперь доступен в виде общедоступной предварительной версии для следующих языков программирования:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node. js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [Дополнительные сведения о пакете SDK для API анализа текста v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Общедоступная Предварительная версия распознавания имен сущностей v3

Дополнительные типы сущностей теперь доступны в общедоступной предварительной версии службы распознавания сущностей (NER) v3, так как мы расширяем обнаружение сущностей общих и личных сведений, найденных в тексте. Это обновление содержит сведения о [версии модели](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`, в том числе:

* Распознавание следующих общих типов сущностей (только на английском языке):
    * персонтипе
    * Продукт
    * Событие
    * Геоадминистративная сущность (ГПЕ) в качестве подтипа в расположении
    * Навык

* Распознавание следующих типов сущностей личных сведений (только на английском языке):
    * Человек
    * План
    * Возраст в качестве подтипа по количеству
    * Дата в качестве подтипа в разделе DateTime
    * Email 
    * Номер телефона (только США)
    * URL-адрес
    * IP-адрес

> [!div class="nextstepaction"]
> [Дополнительные сведения о распознавании имен сущностей v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Октябрь 2019 г.

#### <a name="named-entity-recognition-ner"></a>Распознавание именованных сущностей (NER)

* [Новая конечная точка](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) для распознавания типов сущностей личных сведений (только на английском языке)

* Разделяйте конечные точки для [распознавания сущностей](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) и [связывания сущностей](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Версия модели](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`, которая включает в себя:
    * Расширенное обнаружение и классификация сущностей, найденных в тексте. 
    * Распознавание следующих новых типов сущностей:
        * Номер телефона
        * IP-адрес

Связывание сущностей поддерживает английский и испанский языки. Языковая поддержка NER зависит от типа сущности.

#### <a name="sentiment-analysis-v3-public-preview"></a>Общедоступная предварительная версия 3 анализа тональности

* [Новая конечная точка](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) для анализа тональности.
* [Версия модели](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`, которая включает в себя:

    * Значительные улучшения в точности и подробностях классификации и оценки текста API.
    * Автоматическая маркировка для различных тональности в тексте.
    * Тональности анализ и вывод на уровне документа и предложения. 

Он поддерживает английский (`en`), японский (`ja`), Китайская упрощенная (`zh-Hans`), Китайская традиционная (`zh-Hant`), французский (`fr`), итальянский (`it`), Испанский (`es`), Нидерландский (`nl`), португальский (`pt`) и немецкий (`de`), доступны в следующих регионах: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`и `Southeast Asia`.`South Central US``UK South``West Europe``West US 2` 

> [!div class="nextstepaction"]
> [Дополнительные сведения о анализ тональности v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Следующие шаги

* [Что такое API "Анализ текста"?](overview.md)  
* [Примеры пользовательских сценариев](text-analytics-user-scenarios.md)
* [Пример. Как определить тональность с помощью Анализа текста](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Пример. Как определить язык с помощью Анализа текста](how-tos/text-analytics-how-to-language-detection.md)
* [Распознавание сущностей](how-tos/text-analytics-how-to-entity-linking.md)
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](how-tos/text-analytics-how-to-keyword-extraction.md)
