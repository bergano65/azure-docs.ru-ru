---
title: Использование распознавания сущностей с помощью API анализа текста
titleSuffix: Azure Cognitive Services
description: Learn how to identify and disambiguate the identity of an entity found in text with the Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/21/2019
ms.author: aahi
ms.openlocfilehash: ae5222dcd05740ecb9747037b315c4e920b3eabd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326629"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>How to use Named Entity Recognition in Text Analytics

The [Named Entity Recognition API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) takes unstructured text, and for each JSON document, returns a list of disambiguated entities with links to more information on the web (Wikipedia and Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Связывание сущностей и распознавание именованных сущностей

The Text Analytics' `entities` endpoint supports both named entity recognition (NER) and entity linking.

### <a name="entity-linking"></a>Связывание сущностей
Связывание сущностей позволяет идентифицировать сущности, обнаруженные в тексте, и устранить неоднозначности (например, упоминание слова "Марс" может обозначать планету или римского бога войны). Этот процесс требует наличия базы знаний, с которыми будут связываться распознанные сущности. Например, конечная точка `entities` Текстовой аналитики использует в качестве такой базы знаний сайт Википедии.

### <a name="named-entity-recognition-ner"></a>Распознавание именованных сущностей (NER)
Named entity recognition (NER) is the ability to identify different entities in text and categorize them into pre-defined classes, or types. 

## <a name="named-entity-recognition-v3-public-preview"></a>Named Entity Recognition v3 public preview

The next version of Named Entity Recognition is now available for public preview. It provides updates to both entity linking and Named Entity Recognition. Try it using the [API test console](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral).

:::row:::
    :::column span="":::
        **Компонент**
    :::column-end:::
    ::: column span="":::
        **Описание** 
    :::column-end:::
:::row-end:::
<!-- expanded types and subtypes row-->
:::row:::
    :::column span="":::
        Expanded entity types and subtypes
    :::column-end:::
    :::column span="":::
     Expanded classification and detection for several named entity types.
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        Separate request endpoints 
    :::column-end:::
    :::column span="":::
        Separate endpoints for sending entity linking and NER requests.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` parameter
    :::column-end:::
    :::column span="":::
        An optional parameter for choosing a version of the Text Analytics model. Currently only the default model is available for use.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>Типы сущностей

Named Entity Recognition v3 provides expanded detection across multiple types. Currently, NER v3 can recognize the following categories of entities. For a detailed list of supported entities and languages, see the [Named entity types](../named-entity-types.md) article.

* Общие сведения
* Personal Information 

### <a name="request-endpoints"></a>Request endpoints

Named Entity Recognition v3 uses separate endpoints for NER and entity linking requests. Use a URL format below based on your request:

NER
* General entities - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Personal information entities - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entity linking
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>управления версиями моделей;

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>Supported Types for Named Entity Recognition v2

> [!NOTE]
> The following entities are supported by Named Entity Recognition(NER) version 2. [NER v3](#named-entity-recognition-v3-public-preview) is in public preview, and greatly expands the number and depth of the entities recognized in text.   

| Тип  | SubType | Пример |
|:-----------   |:------------- |:---------|
| Лицо        | Недоступно\*         | "Джеф", "Билл Гейтс"     |
| Location      | Недоступно\*         | "Редмонд, штат Вашингтон", "Париж"  |
| Организация  | Недоступно\*         | "Майкрософт"   |
| Количество      | NUMBER        | "6", "шесть"     |
| Количество      | Процент    | "50 %", "пятьдесят процентов"|
| Количество      | Порядковый номер       | "2-й", "второй"     |
| Количество      | Возраст           | "90 дней", "30 лет"    |
| Количество      | Валюта      | "10,99 долл. США"     |
| Количество      | Измерение     | "10 км", "40 см"     |
| Количество      | Температура   | "32 градуса"    |
| Дата и время      | Недоступно\*         | "18:30 4 февраля 2012 г."      |
| Дата и время      | Дата          | "2 мая 2017 г.", "02.05.2017"   |
| Дата и время      | Время          | "8 утра", "8:00"  |
| Дата и время      | Диапазон дат     | "со 2 по 5 мая"    |
| Дата и время      | Диапазон времени     | "18:00–19:00"     |
| Дата и время      | Duration      | "1 минута 45 секунд"   |
| Дата и время      | Набор           | "каждый вторник"     |
| URL-адрес           | Недоступно\*         | "https:\//www.bing.com"    |
| Эл. почта         | Недоступно\*         | "support@contoso.com" |
| US Phone Number  | Недоступно\*         | (US phone numbers only) "(312) 555-0176" |
| IP-адрес    | Недоступно\*         | "10.0.0.100" |

\* Некоторые сущности могут опускать `SubType` в зависимости от входных и извлеченных сущностей.  All the supported entity types listed are available only for the English, Chinese-Simplified, French, German, and Spanish languages.

### <a name="language-support"></a>Поддержка языков

Связывание сущностей на разных языках требует наличия базы знаний для каждого из этих языков. В Текстовой аналитике это означает, что конечная точка `entities` для распознанных сущностей на каждом поддерживаемом языке предоставляет ссылку на соответствующий раздел Википедии на этом языке. Поскольку наполнение Википедии существенно различается для разных языков, функциональность связывания сущностей также будет иметь существенные различия. See the [language support](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) article for more information.

## <a name="preparation"></a>Подготовка

You must have JSON documents in this format: ID, text, language

Поддерживаемые языки перечислены в [этом списке](../text-analytics-supported-languages.md).

Документ должен содержать менее 5120 символов и иметь до 1000 элементов (идентификаторов) в коллекции. Коллекция передается в тексте запроса. Ниже приведен пример иллюстрации содержимого, который можно отправлять для связывания сущностей.

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Шаг 1. Структурирование запроса

Сведения об определении запроса можно найти в статье [How to call the Text Analytics REST API](text-analytics-how-to-call-api.md) (Способ вызова REST API анализа текста). Для удобства повторим следующие моменты.

+ Создайте запрос **POST**. Review the API documentation for this request: [Entities API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Задайте конечную точку HTTP для извлечения ключевых фраз с помощью ресурса Анализа текста в Azure или экземпляра [контейнера Анализа текста](text-analytics-how-to-install-containers.md). You must include `/text/analytics/v2.1/entities`. Например, `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Set a request header to include [the access key](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) for Text Analytics operations.

+ В тексте запроса укажите набор документов JSON, которые подготовлены для этого анализа

> [!Tip]
> Используйте [Postman](text-analytics-how-to-call-api.md) или откройте **консоль тестирования API** в [документации](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634), чтобы структурировать запрос и передать его (используя метод POST) в службу.

## <a name="step-2-post-the-request"></a>Шаг 2. Передача запроса

Анализ выполняется при получении запроса. Сведения о размере и числе запросов, которые можно отправлять в минуту и секунду, см. в разделе об [ограничениях данных](../overview.md#data-limits).

Не забывайте, что эта служба работает без отслеживания состояния. Данные не хранятся в учетной записи. Результаты возвращаются немедленно в ответе.

## <a name="step-3-view-results"></a>Шаг 3. Просмотр результатов

Все запросы POST вернут ответ в формате JSON с идентификаторами и обнаруженными свойствами.

Вывод возвращается немедленно. Можно выполнить потоковую передачу результатов в приложение, которое принимает JSON, или сохранить выходные данные в файле локальной системы, а затем импортировать их в приложение, которое дает возможность сортировки, поиска данных и управления ими.

Далее показан пример выходных данных для связывания сущностей:

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>Резюме

В этой статье рассматриваются основные понятия и рабочий процесс связывания сущностей в службе Текстовой аналитики Cognitive Services. В разделе "Сводка" сделайте следующее.

+ [Сущности API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) доступны для выбранных языков.
+ В тексте запроса передаются документы JSON, которые содержат идентификатор, текст и код языка.
+ Запрос POST передается в конечную точку `/entities`, используя личный [ключ доступа и конечную точку](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource), допустимые для вашей подписки.
+ Ответ будет содержать выходные данные, содержащие связанные сущности (включая оценки уверенности, смещения и веб-ссылки для каждого идентификатора документа), которые можно использовать в любом приложении

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [API анализа текста](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)
* [Часто задаваемые вопросы](../text-analytics-resource-faq.md)</br>
* [Анализ текста](//go.microsoft.com/fwlink/?LinkID=759712)
