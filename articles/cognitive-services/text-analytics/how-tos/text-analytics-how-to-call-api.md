---
title: Вызов API "Анализ текста"
titleSuffix: Azure Cognitive Services
description: В этой статье объясняется, как можно вызвать Анализ текста Azure Cognitive Services REST API и POST.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: aahi
ms.openlocfilehash: 5985c30973f703b897fa2eedc2be3b939d97900b
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559003"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Как вызвать REST API службы "Анализ текста"

В этой статье мы используем Анализ текста REST API и [POST](https://www.postman.com/downloads/) для демонстрации основных концепций. API предоставляет несколько синхронных и асинхронных конечных точек для использования функций службы. 

## <a name="using-the-api-asynchronously"></a>Асинхронное использование API

Начиная с версии 3.1 – Preview. 3, API анализа текста предоставляет две асинхронные конечные точки: 

* `/analyze`Конечная точка для анализ текста позволяет анализировать один и тот же набор текстовых документов с несколькими функциями текстовой аналитики в одном вызове API. Ранее для использования нескольких функций потребуется создать отдельные вызовы API для каждой операции. Эту возможность следует использовать, если необходимо анализировать большие наборы документов с несколькими Анализ текстаными функциями.

* `/health`Конечная точка для анализ текста работоспособности, которая может извлекать и помечать соответствующие медицинские сведения из документов клинической практике.  

См. таблицу ниже, чтобы узнать, какие функции можно использовать асинхронно. Обратите внимание, что из конечной точки можно вызывать только несколько функций `/analyze` . 

| Компонент | Синхронная | Асинхронный |
|--|--|--|
| Определение языка | ✔ |  |
| Анализ мнений | ✔ |  |
| Интеллектуальный анализ данных | ✔ |  |
| Извлечение ключевой фразы | ✔ | ✔* |
| Распознавание именованных сущностей (включая персональные и фи) | ✔ | ✔* |
| Анализ текста для работоспособности (контейнер) | ✔ |  |
| Анализ текста для работоспособности (API) |  | ✔  |

`*` — Вызывается асинхронно через `/analyze` конечную точку.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>Предварительные требования


> [!NOTE]
> * Если вы хотите использовать [pricing tier](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) `/analyze` `/health` конечные точки или, вам потребуется ресурс анализ текста с помощью ценовой категории Standard (S).

1.  Во-первых, перейдите к [портал Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) и создайте новый ресурс анализ текста, если у вас еще нет такого ресурса. Если вы хотите использовать конечные точки или, выберите **ценовую категорию Standard (S)** `/analyze` `/health` . `/analyze`Конечная точка включена в [ценовую категорию](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

2.  Выберите регион, который вы хотите использовать для конечной точки.  Обратите внимание, что `/analyze` `/health` конечные точки и доступны только в следующих регионах: Западная часть США 2, Восточная часть США 2, Центральная часть США, Северная Европа и Западная Европа.

3.  Создайте ресурс Анализ текста и перейдите в колонку "ключи и конечная точка" в левой части страницы. Скопируйте ключ, который будет использоваться позже при вызове API. Вы добавите его позже в качестве значения для `Ocp-Apim-Subscription-Key` заголовка.


<a name="json-schema"></a>

## <a name="api-request-format"></a>Формат запроса API

#### <a name="synchronous"></a>[Синхронная](#tab/synchronous)

Формат запросов API одинаков для всех синхронных операций. Документы отправляются в объекте JSON как необработанный неструктурированный текст. XML не поддерживается. Схема JSON состоит из элементов, описанных ниже.

| Элемент | Допустимые значения | Необходим? | Использование |
|---------|--------------|-----------|-------|
|`id` |Типом данных выступает строка, однако обычно идентификаторы документов являются целыми числами. | Обязательно | Система использует предоставленные идентификаторы, чтобы структурировать выходные данные. Коды языков, ключевые фразы и оценка тональности создаются для каждого идентификатора в запросе.|
|`text` | Неструктурированный необработанный текст, не более 5 120 символов. | Обязательно | Для распознавания языка текст может быть выражен на любом языке. Для анализа тональности, извлечения ключевых фраз и идентификации сущности текст должен быть на [поддерживаемом языке](../language-support.md). |
|`language` | Двухзначный код [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) для [поддерживаемого языка](../language-support.md). | Различается | Требуется для анализа тональности, извлечения ключевых фраз и связывания сущностей. Необязательно для распознавания языка. Код можно исключить, и это не будет ошибкой, однако без него анализ будет менее точным. Код языка должен соответствовать предоставленному вами элементу `text`. |

Ниже приведен пример запроса API для синхронных Анализ текста конечных точек. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="analyze"></a>[Анализ](#tab/analyze)

> [!NOTE]
> Последняя Предварительная версия клиентской библиотеки Анализ текста позволяет вызывать асинхронные операции анализа с помощью клиентского объекта. Примеры можно найти на сайте GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

`/analyze`Конечная точка позволяет выбрать, какие из поддерживаемых функций анализ текста будут использоваться в одном вызове API. Сейчас эта конечная точка поддерживает:

* извлечение ключевых фраз 
* Распознавание именованных сущностей (включая персональные и фи)

| Элемент | Допустимые значения | Необходим? | Использование |
|---------|--------------|-----------|-------|
|`displayName` | Строка | Необязательно | Используется в качестве отображаемого имени для уникального идентификатора задания.|
|`analysisInput` | Включает `documents` поле ниже | Обязательно | Содержит сведения о документах, которые необходимо отправить. |
|`documents` | Включает `id` поля и `text` ниже | Обязательно | Содержит сведения для каждого отправляемого документа и необработанный текст документа. |
|`id` | Строка | Обязательно | Предоставленные идентификаторы используются для структурирования выходных данных. |
|`text` | Неструктурированный необработанный текст, не более 125 000 символов. | Обязательно | Язык должен быть на английском языке, который в настоящее время поддерживается. |
|`tasks` | Включает следующие функции Анализ текста: `entityRecognitionTasks` `keyPhraseExtractionTasks` или `entityRecognitionPiiTasks` . | Обязательно | Одна или несколько Анализ текста функций, которые вы хотите использовать. Обратите внимание, что `entityRecognitionPiiTasks` имеет необязательный `domain` параметр, для которого можно задать значение `pii` или `phi` . Если значение не указано, по умолчанию система принимает значение `pii` . |
|`parameters` | Включает `model-version` поля и `stringIndexType` ниже | Обязательно | Это поле включено в указанные выше задачи функции. Они содержат сведения о версии модели, которую необходимо использовать, и типе индекса. |
|`model-version` | Строка | Обязательно | Укажите версию вызываемой модели, которую необходимо использовать.  |
|`stringIndexType` | Строка | Обязательно | Укажите текстовый декодер, соответствующий используемой среде программирования.  Поддерживаются типы `textElement_v8` (по умолчанию), `unicodeCodePoint` , `utf16CodeUnit` . Дополнительные сведения см. в [статье о смещениях текста](../concepts/text-offsets.md#offsets-in-api-version-31-preview) .  |
|`domain` | Строка | Необязательно | Применяется только в качестве параметра к `entityRecognitionPiiTasks` задаче и может иметь значение `pii` или `phi` . Значение по умолчанию —, `pii` если не указано.  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

#### <a name="text-analytics-for-health"></a>[Анализ текста для сферы здравоохранения](#tab/health)

Формат запросов API к Анализ текста для API, размещенного в работоспособности, аналогичен его контейнеру. Документы отправляются в объекте JSON как необработанный неструктурированный текст. XML не поддерживается. Схема JSON состоит из элементов, описанных ниже.  Заполните и отправьте [форму запроса Cognitive Services](https://aka.ms/csgate) , чтобы запросить доступ к анализ текста для общедоступной предварительной версии работоспособности. Вам не будет выставлен счет за Анализ текста для использования работоспособности. 

| Элемент | Допустимые значения | Необходим? | Использование |
|---------|--------------|-----------|-------|
|`id` |Типом данных выступает строка, однако обычно идентификаторы документов являются целыми числами. | Обязательно | Система использует предоставленные идентификаторы, чтобы структурировать выходные данные. |
|`text` | Неструктурированный необработанный текст, не более 5 120 символов. | Обязательно | Обратите внимание, что в настоящее время поддерживается только английский текст. |
|`language` | Двухзначный код [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) для [поддерживаемого языка](../language-support.md). | Обязательно | `en`Поддерживается только в настоящее время. |

Ниже приведен пример запроса API для Анализ текста конечных точек работоспособности. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Сведения о тарифах и ограничениях размера для отправки данных в API анализа текста см. в статье [ограничения скорости и данных](../concepts/data-limits.md) .


## <a name="set-up-a-request"></a>Настройка запроса 

В POST (или другом средстве тестирования веб-API) добавьте конечную точку для компонента, который вы хотите использовать. Используйте приведенную ниже таблицу, чтобы найти соответствующий формат конечной точки и заменить `<your-text-analytics-resource>` его конечной точкой ресурса. Пример:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Синхронная](#tab/synchronous)

| Компонент | Тип запроса | Конечные точки ресурсов |
|--|--|--|
| Определение языка | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Анализ мнений | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Интеллектуальный анализ мнений | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Извлечение ключевой фразы | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Распознавание именованных сущностей — общие | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Распознавание именованных сущностей — PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Распознавание именованных сущностей — фи | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="analyze"></a>[Анализ](#tab/analyze)

| Компонент | Тип запроса | Конечные точки ресурсов |
|--|--|--|
| Отправить задание анализа | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| Получение состояния и результатов анализа | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

#### <a name="text-analytics-for-health"></a>[Анализ текста для сферы здравоохранения](#tab/health)

| Компонент | Тип запроса | Конечные точки ресурсов |
|--|--|--|
| Отправка Анализ текста для задания работоспособности  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| Получение состояния задания и результатов | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| отмена задания. | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

После создания конечной точки в POST (или другом средстве тестирования веб-API):

1. Выберите тип запроса для компонента, который вы хотите использовать.
2. Вставьте в конечную точку требуемой операции из приведенной выше таблицы.
3. Задайте три заголовка запроса:

   + `Ocp-Apim-Subscription-Key`: ключ доступа, полученный из портал Azure
   + `Content-Type`: application/json
   + `Accept`: application/json

    Если вы используете POST, ваш запрос должен выглядеть примерно так, как на следующем снимке экрана, предполагая, что `/keyPhrases` Конечная точка.
    
    ![Снимок экрана запроса с конечной точкой и заголовками](../media/postman-request-keyphrase-1.png)
    
4. Выберите **необработанный** формат **текста**
    
    ![Снимок экрана запроса с параметрами текста](../media/postman-request-body-raw.png)

5. Вставьте в некоторые документы JSON в допустимом формате. Используйте примеры в разделе **Формат запроса API** выше, а дополнительные сведения и примеры см. в следующих разделах:

      + [Пример. Как определить язык с помощью Анализа текста](text-analytics-how-to-language-detection.md)
      + [Пример. Как извлечь ключевые фразы с помощью Анализа текста](text-analytics-how-to-keyword-extraction.md)
      + [Пример. Как определить тональность с помощью Анализа текста](text-analytics-how-to-sentiment-analysis.md)
      + [Распознавание сущностей](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Отправка запроса

Отправьте запрос API. Если вызвать синхронную конечную точку, ответ будет отображен немедленно, как один документ JSON с элементом для каждого идентификатора документа, указанного в запросе.

Если вы выполнили вызов асинхронной `/analyze` или `/health` конечной точки, убедитесь, что получен код ответа 202. чтобы просмотреть результаты, необходимо получить ответ:

1. В ответе API найдите `Operation-Location` из заголовка, который определяет задание, отправленное в API. 
2. Создайте запрос GET для используемой конечной точки. см. приведенную [выше таблицу](#set-up-a-request) для формата конечной точки и просмотрите [справочную документацию по API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Пример:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. Добавьте в `Operation-Location` запрос.

4. Ответ будет отдельным документом JSON с элементом для каждого идентификатора документа, указанного в запросе.

## <a name="example-api-responses"></a>Примеры ответов API
 
# <a name="synchronous"></a>[Синхронная](#tab/synchronous)

Синхронные ответы конечных точек будут зависеть от используемой конечной точки. Примеры ответов см. в следующих статьях.

+ [Пример. Как определить язык с помощью Анализа текста](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Пример. Как извлечь ключевые фразы с помощью Анализа текста](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Пример. Как определить тональность с помощью Анализа текста](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Распознавание сущностей](text-analytics-how-to-entity-linking.md#view-results)

# <a name="analyze"></a>[Анализ](#tab/analyze)

В случае успешного выполнения запрос GET к `/analyze` конечной точке возвратит объект, содержащий назначенные задачи. Например, `keyPhraseExtractionTasks`. Эти задачи содержат объект Response из соответствующей Анализ текста функции. Дополнительные сведения см. в приведенных ниже статьях.

+ [Пример. Как извлечь ключевые фразы с помощью Анализа текста](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Распознавание сущностей](text-analytics-how-to-entity-linking.md#view-results)


```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```

# <a name="text-analytics-for-health"></a>[Анализ текста для сферы здравоохранения](#tab/health)

Дополнительные сведения о Анализ текста для асинхронного ответа API работоспособности см. в следующей статье:

+ [Анализ текста для сферы здравоохранения](text-analytics-for-health.md#hosted-asynchronous-web-api-response)


--- 

## <a name="see-also"></a>См. также раздел

* [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)
* [Часто задаваемые вопросы](../text-analytics-resource-faq.md)</br>
* [Анализ текста](//go.microsoft.com/fwlink/?LinkID=759712)
* [Использование клиентской библиотеки Анализа текста](../quickstarts/text-analytics-sdk.md)
* [Новые возможности](../whats-new.md)
