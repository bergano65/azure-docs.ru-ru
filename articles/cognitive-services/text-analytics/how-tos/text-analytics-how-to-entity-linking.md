---
title: Использование распознавания сущностей с помощью API анализа текста
titleSuffix: Azure Cognitive Services
description: Узнайте, как идентифицировать и устранить неоднозначность удостоверения сущности, найденной в тексте с помощью REST API Анализ текста.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/12/2019
ms.author: aahi
ms.openlocfilehash: 5933c7ec56ded971e4daf96ea6d4302c04921f2f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031427"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Как использовать распознавание именованных сущностей в Анализ текста

[Именованный API распознавания сущностей](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) принимает неструктурированный текст, и для каждого документа JSON возвращает список неоднозначности сущностей со ссылками на дополнительные сведения в Интернете (Википедии и Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Связывание сущностей и распознавание именованных сущностей

Конечная точка `entities` Анализ текста "поддерживает распознавание имен сущностей (NER) и связывание сущностей.

### <a name="entity-linking"></a>Связывание сущностей
Связывание сущностей позволяет идентифицировать сущности, обнаруженные в тексте, и устранить неоднозначности (например, упоминание слова "Марс" может обозначать планету или римского бога войны). Этот процесс требует наличия базы знаний, с которыми будут связываться распознанные сущности. Например, конечная точка `entities` Текстовой аналитики использует в качестве такой базы знаний сайт Википедии.

### <a name="named-entity-recognition-ner"></a>Распознавание именованных сущностей (NER)
Распознавание имен сущностей (NER) — это возможность определять различные сущности в тексте и классифицировать их в заранее определенные классы или типы. 

## <a name="named-entity-recognition-v3-public-preview"></a>Общедоступная Предварительная версия распознавания имен сущностей v3

[Следующая версия распознавания имен сущностей]( https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) теперь доступна для общедоступной предварительной версии. Он предоставляет обновления для связывания сущностей и распознавания именованных сущностей. 

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
        Расширенные типы сущностей и подтипы
    :::column-end:::
    :::column span="":::
     Расширенная классификация и обнаружение для нескольких типов именованных сущностей.
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        Отдельные конечные точки запроса 
    :::column-end:::
    :::column span="":::
        Отдельные конечные точки для отправки запросов на связывание сущностей и NER.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version`, параметр
    :::column-end:::
    :::column span="":::
        Необязательный параметр для выбора версии модели Анализ текста. В настоящее время для использования доступна только модель по умолчанию.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>Типы сущностей

Распознавание именованных сущностей v3 обеспечивает Расширенное обнаружение для нескольких типов. В настоящее время NER v3 может распознать следующие категории сущностей. Подробный список поддерживаемых сущностей и языков см. в статье [именованные типы сущностей](../named-entity-types.md) .

* Общие
* Персональные данные 

### <a name="request-endpoints"></a>Конечные точки запроса

В примере распознавания сущностей v3 используются отдельные конечные точки для запросов NER и связывания сущностей. Используйте следующий формат URL-адреса в зависимости от вашего запроса:

NER
* Общие сущности — `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Сущности с личными сведениями — `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Связывание сущностей
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>управления версиями моделей;

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>Поддерживаемые типы для распознавания именованных сущностей версии 2

> [!NOTE]
> Следующие сущности поддерживаются с помощью средства распознавания сущностей (NER) версии 2. [NER v3](#named-entity-recognition-v3-public-preview) находится в общедоступной предварительной версии и значительно расширяет количество и глубину сущностей, распознаваемых в тексте.   

| введите  | SubType | Пример |
|:-----------   |:------------- |:---------|
| Person        | Недоступно\*         | "Джеф", "Билл Гейтс"     |
| Место проведения      | Недоступно\*         | "Редмонд, штат Вашингтон", "Париж"  |
| План  | Недоступно\*         | "Майкрософт"   |
| Количество      | Число        | "6", "шесть"     |
| Количество      | Процент    | "50 %", "пятьдесят процентов"|
| Количество      | Порядковый номер       | "2-й", "второй"     |
| Количество      | Age           | "90 дней", "30 лет"    |
| Количество      | Валюта      | "10,99 долл. США"     |
| Количество      | Измерение     | "10 миль", "40 см"     |
| Количество      | температура;   | "32 градуса"    |
| DateTime      | Недоступно\*         | "18:30 4 февраля 2012 г."      |
| DateTime      | Дата          | "2 мая 2017 г.", "02.05.2017"   |
| DateTime      | Время          | "8 утра", "8:00"  |
| DateTime      | Диапазон дат     | "с 2 по 5 мая"    |
| DateTime      | Диапазон времени     | "6:00–17:00"     |
| DateTime      | Длительность      | "1 минута и 45 секунд"   |
| DateTime      | Set           | "каждый вторник"     |
| URL-адрес           | Недоступно\*         | "https:\//www.bing.com"    |
| Email         | Недоступно\*         | "support@contoso.com" |

\* Некоторые сущности могут опускать `SubType` в зависимости от входных и извлеченных сущностей.  Все поддерживаемые типы сущностей доступны только для английского, упрощенного, французского, немецкого и испанского языков.

### <a name="language-support"></a>Поддержка языков

Связывание сущностей на разных языках требует наличия базы знаний для каждого из этих языков. В Текстовой аналитике это означает, что конечная точка `entities` для распознанных сущностей на каждом поддерживаемом языке предоставляет ссылку на соответствующий раздел Википедии на этом языке. Поскольку наполнение Википедии существенно различается для разных языков, функциональность связывания сущностей также будет иметь существенные различия. Дополнительные сведения см. в статье [Поддержка языков](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) .

## <a name="preparation"></a>Подготовка

Необходимо иметь документы JSON в следующем формате: ID, Text, Language.

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

## <a name="step-1-structure-the-request"></a>Шаг 1: Структура запроса

Сведения об определении запроса можно найти в статье [How to call the Text Analytics REST API](text-analytics-how-to-call-api.md) (Способ вызова REST API анализа текста). Для удобства повторим следующие моменты.

+ Создайте запрос **POST**. Ознакомьтесь с документацией по API для этого запроса: [СУЩНОСТИ API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Задайте конечную точку HTTP для извлечения ключевых фраз с помощью ресурса Анализа текста в Azure или экземпляра [контейнера Анализа текста](text-analytics-how-to-install-containers.md). Необходимо включить `/text/analytics/v2.1/entities`. Например, `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Задайте заголовок запроса, чтобы включить [ключ доступа](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) для операций анализ текста.

+ В тексте запроса укажите набор документов JSON, которые подготовлены для этого анализа

> [!Tip]
> Используйте [Postman](text-analytics-how-to-call-api.md) или откройте **консоль тестирования API** в [документации](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634), чтобы структурировать запрос и передать его (используя метод POST) в службу.

## <a name="step-2-post-the-request"></a>Шаг 2. Передача запроса

Анализ выполняется при получении запроса. Сведения о размере и числе запросов, которые можно отправлять в минуту и секунду, см. в разделе об [ограничениях данных](../overview.md#data-limits).

Не забывайте, что эта служба работает без отслеживания состояния. Данные не хранятся в учетной записи. Результаты возвращаются немедленно в ответе.

## <a name="step-3-view-results"></a>Шаг 3. Просмотр результатов

Все запросы POST вернут ответ в формате JSON с идентификаторами и обнаруженными свойствами.

Вывод возвращается немедленно. Можно выполнить потоковую передачу результатов в приложение, которое принимает JSON или сохранить выходные данные в файле локальной системы, а затем импортировать их в приложение, которое дает возможность сортировки, поиска и управления данными.

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

## <a name="summary"></a>summary

В этой статье рассматриваются основные понятия и рабочий процесс связывания сущностей в службе Текстовой аналитики Cognitive Services. Краткая сводка.

+ [Сущности API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) доступны для выбранных языков.
+ В тексте запроса передаются документы JSON, которые содержат идентификатор, текст и код языка.
+ Отправьте запрос POST на конечную точку `/entities`, используя правильные значения [ключа доступа и конечной точки](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) из вашей подписки.
+ Ответ будет содержать выходные данные, содержащие связанные сущности (включая оценки уверенности, смещения и веб-ссылки для каждого идентификатора документа), которые можно использовать в любом приложении

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [API анализа текста](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Text Analytics overview](../overview.md) (Общие сведения о Текстовой аналитике)
* [Часто задаваемые вопросы](../text-analytics-resource-faq.md)</br>
* [Анализ текста](//go.microsoft.com/fwlink/?LinkID=759712)
