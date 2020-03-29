---
title: Что нового в API для аналитики текста
titleSuffix: Text Analytics - Azure Cognitive Services
description: В этой статье содержится информация о новых выпусках и функциях для текстовой аналитики Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77188800"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Новые возможности в API "Анализ текста"

API Text Analytics обновляется на постоянной основе. Чтобы быть в курсе последних событий, эта статья предоставляет вам информацию о новых релизах и функциях.

## <a name="february-2020"></a>Февраль 2020 г.

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Поддержка SDK для текстовых Аналитика API v3 Публичный Предварительный

В рамках [единого выпуска Azure SDK](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)API aPI v3 SDK теперь доступен в качестве общедоступного предварительного просмотра следующих языков программирования:
   * [C #](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [Узнайте больше о Text Analytics API v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Названное признание сущности v3 публичный предварительный просмотр

Дополнительные типы сущностей теперь доступны в службе публичного предварительного просмотра Named Entity (NER) v3 по мере расширения обнаружения общих и личных информационных сущностей, найденных в тексте. Это обновление представляет [модельную версию,](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`которая включает в себя:

* Признание следующих типов общих сущностей (только на английском языке):
    * ПерсонТип
    * Продукт
    * Событие
    * Геополитическое образование (GPE) как подтип под местоположением
    * Навык

* Признание следующих типов сущностей личной информации (только на английском языке):
    * Модель Person
    * План
    * Возраст как подтип под количественное время
    * Дата как подтип под dateTime
    * Email 
    * Номер телефона (только для США)
    * URL-адрес
    * IP-адрес

> [!div class="nextstepaction"]
> [Узнайте больше о Именованном признании сущности v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Октябрь 2019 г.

#### <a name="named-entity-recognition-ner"></a>Распознавание именованных сущностей (NER)

* [Новая конечная точка](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) для распознавания типов сущностей личной информации (только на английском языке)

* Отдельные конечные точки для [распознавания сущности](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) и [связывания сущности.](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)

* [Модельная версия](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`, которая включает в себя:
    * Расширенное обнаружение и классификация объектов, найденных в тексте. 
    * Распознавание следующих новых типов сущностей:
        * номер телефона.
        * IP-адрес

Сущность ссылок поддерживает английский и испанский языки. Поддержка языка NER зависит от типа сущности.

#### <a name="sentiment-analysis-v3-public-preview"></a>Общедоступная предварительная версия 3 анализа тональности

* [Новая конечная точка](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) для анализа настроений.
* [Модельная версия](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`, которая включает в себя:

    * Значительное улучшение точности и детализации классификации текста API и скоринга.
    * Автоматическая маркировка для различных настроений в тексте.
    * Анализ настроений и выход на уровне документа и предложения. 

Он поддерживает`en`английский язык`ja`( ,`zh-Hans`японский`zh-Hant`( ),`fr`Китайский`it`Упрощенный`es`(`nl`), Китайский Традиционный ( ), Французский ( ), Итальянский ( ), Испанский ( ), Голландский ( ,`pt`Португальский (, и немецкий`de`(), и доступен в следующих регионах: `Australia East`, `Central Canada` `Central US` `East Asia`, `East US`, `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South`, , , , `West Europe`, , `West US 2` 

> [!div class="nextstepaction"]
> [Узнайте больше об анализе настроений v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое API "Анализ текста"?](overview.md)  
* [Примеры пользовательских сценариев](text-analytics-user-scenarios.md)
* [Анализ тональности](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Обнаружение языка](how-tos/text-analytics-how-to-language-detection.md)
* [Распознавание сущности](how-tos/text-analytics-how-to-entity-linking.md)
* [Извлечение ключевых фраз](how-tos/text-analytics-how-to-keyword-extraction.md)
