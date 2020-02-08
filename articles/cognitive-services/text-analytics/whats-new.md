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
ms.openlocfilehash: e3fbff7a5919d4d43656f3112cb24f2017191348
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086790"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Новые возможности в API "Анализ текста"

API анализа текста обновляется на постоянной основе. В этой статье содержатся сведения о новых выпусках и функциях, чтобы оставаться в курсе последних нововведений.

## <a name="named-entity-recognition-v3-public-preview---february-2020"></a>Распознавание именованных сущностей v3 — общедоступная Предварительная версия — Февраль 2020

Дополнительные типы сущностей теперь доступны в общедоступной предварительной версии службы распознавания сущностей (NER) v3, так как мы расширяем обнаружение сущностей общих и личных сведений, найденных в тексте. Это обновление содержит сведения о [версии модели](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`, в том числе:

* Распознавание следующих общих типов сущностей (только на английском языке):
    * персонтипе
    * Продукт
    * Событие
    * Геоадминистративная сущность (ГПЕ) в качестве подтипа в расположении
    * Навык

* Распознавание следующих типов сущностей личных сведений (только на английском языке):
    * Модель Person
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
        * номер телефона.
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

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое API "Анализ текста"?](overview.md)  
* [Примеры пользовательских сценариев](text-analytics-user-scenarios.md)
* [Пример. Как определить тональность с помощью Анализа текста](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Пример. Как определить язык с помощью Анализа текста](how-tos/text-analytics-how-to-language-detection.md)
* [Распознавание сущностей](how-tos/text-analytics-how-to-entity-linking.md)
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](how-tos/text-analytics-how-to-keyword-extraction.md)
