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
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: 1a5f7dcd37a970d5e039ebc38f1059839424ff68
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192024"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Новые возможности в API "Анализ текста"

API анализа текста обновляется на постоянной основе. В этой статье содержатся сведения о новых выпусках и функциях, чтобы оставаться в курсе последних нововведений.

## <a name="august-2020"></a>Август 2020 г.

### <a name="general-api-updates"></a>Общие обновления API

* Версия модели `2020-07-01` для v3 `/keyphrases` , `/pii` а также `/languages` конечные точки, которые добавляют:
    * Дополнительные [категории сущностей](named-entity-types.md?tabs=personal) для государственных организаций и страны для распознавания именованных сущностей.
* Теперь будет возвращена ошибка HTTP 400 для запросов API V3, превышающих [пределы](concepts/data-limits.md)опубликованных данных. 

### <a name="text-analytics-for-health-container-august-updates"></a>Анализ текста обновлений для контейнера работоспособности за Август

Следующие обновления относятся только к выпуску Анализ текста за Август только для контейнера работоспособности.

* Новая модель — версия для Анализ текста работоспособности: `2020-07-24`
* Новый URL-адрес для отправки Анализ текста для запросов на работоспособность: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (Обратите внимание, что для использования демонстрационного веб-приложения, входящего в этот новый образ контейнера, требуется очистка кэша браузера)

Изменены следующие свойства в ответе JSON:

* `type` был переименован в `category`. 
* `score` был переименован в `confidenceScore`.
* Сущности в `category` поле выходных данных JSON теперь находятся в стиле Pascal. Следующие сущности были переименованы:
    * `EXAMINATION_RELATION` переименован в `RelationalOperator`.
    * `EXAMINATION_UNIT` переименован в `MeasurementUnit`.
    * `EXAMINATION_VALUE` переименован в `MeasurementValue`.
    * `ROUTE_OR_MODE` был переименован `MedicationRoute` .
    * Реляционная сущность `ROUTE_OR_MODE_OF_MEDICATION` была переименована в `RouteOfMedication` .

Добавлены следующие сущности:

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Извлечение связей
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [Дополнительные сведения о Анализ текста контейнере работоспособности](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>Июль 2020 г. 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Анализ текста для контейнера работоспособности — Предварительная версия с открытой проверкой

Анализ текста для контейнера работоспособности теперь находится в общедоступной предварительной версии, которая позволяет извлекать сведения из неструктурированного текста на английском языке в клинической практике документах, таких как формы пациента принимать, заметки Doctor, справочные материалы и сводки о разрядах. В настоящее время не взимается плата за Анализ текста для использования контейнеров работоспособности.

Контейнер предоставляет следующие возможности:

* Распознавание именованных сущностей
* Извлечение связей
* Связывание сущностей
* Отрицание

## <a name="may-2020"></a>Май 2020 г.

### <a name="text-analytics-api-v3-general-availability"></a>Общая доступность API анализа текста v3

API анализа текста v3 теперь общедоступен со следующими обновлениями:

* Версия модели `2020-04-01`
* Новые [ограничения данных](concepts/data-limits.md) для каждого компонента
* Обновленная [языковая поддержка](language-support.md) для [Анализ тональности (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Отдельная конечная точка для связывания сущностей 
* Новая категория сущностей "адрес" в [распознавании имен сущностей (NER) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Новые подкатегории в NER V3:
   * Location — География
   * Расположение — структурное
   * Организация — обмен на бирже
   * Организация — медицинских
   * Организация — Спорт
   * Событие — язык и региональные стандарты
   * Естественное событие
   * Событие — Спорт

Были добавлены следующие свойства в ответе JSON:
   * `SentenceText` в анализ тональности
   * `Warnings` для каждого документа 

Имена следующих свойств в ответе JSON были изменены, где это применимо:

* `score` был переименован в `confidenceScore`.
    * `confidenceScore` имеет две десятичные точки точности. 
* `type` был переименован в `category`.
* `subtype` был переименован в `subcategory`.

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [Дополнительные сведения о API анализа текста v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Общедоступная Предварительная версия API анализа текста версии 3.1
   * Новое анализ тональности функции — [интеллектуальный анализ данных](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Новый [личный `PII` фильтр домена ()](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) для защищенных сведений о работоспособности ( `PHI` ).

> [!div class="nextstepaction"]
> [Дополнительные сведения о предварительной версии API анализа текста v 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Февраль 2020 г.

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Поддержка пакета SDK для общедоступной предварительной версии API анализа текста v3

В рамках [единой версии пакета SDK для Azure](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)пакет sdk для API анализа текста v3 теперь доступен в виде общедоступной предварительной версии для следующих языков программирования:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [Дополнительные сведения о пакете SDK для API анализа текста v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Общедоступная Предварительная версия распознавания имен сущностей v3

Дополнительные типы сущностей теперь доступны в общедоступной предварительной версии службы распознавания сущностей (NER) v3, так как мы расширяем обнаружение сущностей общих и личных сведений, найденных в тексте. В этом обновлении представлена [версия модели](concepts/model-versioning.md) `2020-02-01` , которая включает в себя:

* Распознавание следующих общих типов сущностей (только на английском языке):
    * персонтипе
    * Продукт
    * Событие
    * Геоадминистративная сущность (ГПЕ) в качестве подтипа в расположении
    * Навык

* Распознавание следующих типов сущностей личных сведений (только на английском языке):
    * Модель Person
    * Организация
    * Возраст в качестве подтипа по количеству
    * Дата в качестве подтипа в разделе DateTime
    * Адрес электронной почты 
    * Номер телефона (только США)
    * URL-адрес
    * IP-адрес

> [!div class="nextstepaction"]
> [Дополнительные сведения о распознавании имен сущностей v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Октябрь 2019 г.

#### <a name="named-entity-recognition-ner"></a>Распознавание именованных сущностей (NER)

* [Новая конечная точка](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) для распознавания типов сущностей личных сведений (только на английском языке)

* Разделяйте конечные точки для [распознавания сущностей](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) и [связывания сущностей](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Версия модели](concepts/model-versioning.md) `2019-10-01` , которая включает в себя:
    * Расширенное обнаружение и классификация сущностей, найденных в тексте. 
    * Распознавание следующих новых типов сущностей:
        * Номер телефона
        * IP-адрес

Связывание сущностей поддерживает английский и испанский языки. Языковая поддержка NER зависит от типа сущности.

#### <a name="sentiment-analysis-v3-public-preview"></a>Общедоступная предварительная версия 3 анализа тональности

* [Новая конечная точка](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) для анализа тональности.
* [Версия модели](concepts/model-versioning.md) `2019-10-01` , которая включает в себя:

    * Значительные улучшения в точности и подробностях классификации и оценки текста API.
    * Автоматическая маркировка для различных тональности в тексте.
    * Тональности анализ и вывод на уровне документа и предложения. 

Он поддерживает английский ( `en` ), японский ( `ja` ), китайский упрощенный () `zh-Hans` , китайский (традиционное письмо `zh-Hant` ), французский ( `fr` ), итальянский ( `it` ), Испанский ( `es` ), Нидерландский ( `nl` ), португальский () и `pt` немецкий ( `de` ) и доступен в следующих регионах:,,,,, `Australia East` `Central Canada` `Central US` `East Asia` `East US` `East US 2` , `North Europe` , `Southeast Asia` ,,, `South Central US` `UK South` `West Europe` и `West US 2` . 

> [!div class="nextstepaction"]
> [Дополнительные сведения о анализ тональности v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Дальнейшие шаги

* [Что такое API "Анализ текста"?](overview.md)  
* [Примеры пользовательских сценариев](text-analytics-user-scenarios.md)
* [Пример. Как определить тональность с помощью Анализа текста](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Пример. Как определить язык с помощью Анализа текста](how-tos/text-analytics-how-to-language-detection.md)
* [Распознавание сущностей](how-tos/text-analytics-how-to-entity-linking.md)
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](how-tos/text-analytics-how-to-keyword-extraction.md)
