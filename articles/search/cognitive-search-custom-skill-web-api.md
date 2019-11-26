---
title: Custom Web API skill in skillsets
titleSuffix: Azure Cognitive Search
description: Extend capabilities of Azure Cognitive Search skillsets by calling out to Web APIs. Use the Custom Web API skill to integrate your custom code.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 29928d78c2cfc2f21def363341f8383c4efa89d2
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484115"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Custom Web API skill in an Azure Cognitive Search enrichment pipeline

The **Custom Web API** skill allows you to extend AI enrichment by calling out to a Web API endpoint providing custom operations. Аналогично встроенным навыкам **пользовательский навык веб-API** имеет входные и выходные данные. Depending on the inputs, your Web API receives a JSON payload when the indexer runs, and outputs a JSON payload as a response, along with a success status code. В ответе должны содержаться выходные данные, указанные в вашем пользовательском навыке. Любой другой ответ считается ошибкой, и никакие обогащения не выполняются.

Структура полезных данных JSON описана далее в этом документе.

> [!NOTE]
> Индексатор будет дважды повторять попытки для определенных стандартных кодов состояния HTTP, возвращаемых из веб-API. Это такие коды состояния HTTP: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Имя параметра     | Описание |
|--------------------|-------------|
| uri | The URI of the Web API to which the _JSON_ payload will be sent. Допускается только схема URI **HTTPS**. |
| httpMethod | Метод, используемый при отправке полезных данных. Допустимые методы: `PUT` или `POST`. |
| httpHeaders | Коллекция пар "ключ — значение", в которой ключи представляют имена заголовков, а значения — значения заголовков, которые будут отправляться в веб-API вместе с полезными данными. В этой коллекции запрещены следующие заголовки: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via`. |
| timeout | (Необязательно.) Если указано, означает время ожидания вызова API HTTP-клиента. Значение должно быть отформатировано как значение dayTimeDuration XSD (ограниченное подмножество значения [продолжительности ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Например, `PT60S` для 60 секунд. Если не задано, выбирается значение по умолчанию — 30 секунд. The timeout can be set to a maximum of 230 seconds and a minimum of 1 second. |
| batchSize | (Необязательно.) Указывает, сколько "записей данных" (см. в разделе о структуре полезных данных _JSON_ ниже) будет отправлено на один вызов API. В противном случае выбирается значение по умолчанию — 1000. Мы советуем вам использовать этот параметр для достижения подходящего баланса между пропускной способностью индексации и нагрузкой на API. |
| degreeOfParallelism | (Optional) When specified, indicates the number of calls the indexer will make in parallel to the endpoint you have provided. You can decrease this value if your endpoint is failing under too high of a request load, or raise it if your endpoint is able to accept more requests and you would like an increase in the performance of the indexer.  If not set, a default value of 5 is used. The degreeOfParallelism can be set to a maximum of 10 and a minimum of 1. |

## <a name="skill-inputs"></a>Входные данные навыков

Предварительно заданные входные данные для этого навыка отсутствуют. Вы можете выбрать одно или несколько полей, которые уже будут доступны во время выполнения этого навыка в качестве входных данных. Полезные данные _JSON_, отправляемые в веб-API, будут иметь разные поля.

## <a name="skill-outputs"></a>Выходные данные навыка

Предварительно заданные выходные данные для этого навыка отсутствуют. В зависимости от ответа, который вернет веб-API, добавьте поля вывода, чтобы их можно было получить из ответа _JSON_.


## <a name="sample-definition"></a>Пример определения

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Пример структуры входных данных JSON

Эта структура _JSON_ представляет полезные данные, которые будут отправляться в веб-API.
Она всегда будет соответствовать этим ограничениям:

* Сущность верхнего уровня называется `values` и будет массивом объектов. Количество таких объектов не будет превышать `batchSize`.
* Каждый объект в массиве `values` будет иметь:
    * Свойство `recordId`, которое является **уникальной** строкой, используемой для идентификации этой записи.
    * Свойство `data`, которое является объектом _JSON_. Поля свойства `data` будут соответствовать именам, указанным в разделе `inputs` определения навыков. Значения этих полей будут взяты из источника (`source`) этих полей (например, из поля в документе или, возможно, из другого навыка).

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Пример структуры выходных данных JSON

The "output" corresponds to the response returned from your Web API. The Web API should only return a _JSON_ payload (verified by looking at the `Content-Type` response header) and should satisfy the following constraints:

* Должна существовать сущность верхнего уровня, называемая `values`, которая должна быть массивом объектов.
* The number of objects in the array should be the same as the number of objects sent to the Web API.
* Каждый объект должен иметь:
   * Свойство `recordId`.
   * Свойство `data`, являющееся объектом, в котором поля представляют собой обогащения, соответствующие именам в `output` (их значения считаются обогащением).
   * Свойство `errors` — массив, перечисляющий все обнаруженные ошибки, добавляемые в журнал выполнения индексатора. Это свойство является обязательным, но может иметь значение `null`.
   * Свойство `warnings` — массив, перечисляющий все обнаруженные предупреждения, добавляемые в журнал выполнения индексатора. Это свойство является обязательным, но может иметь значение `null`.
* Объекты в массиве `values` не обязательно должны находиться в том же порядке, что и объекты в массиве `values`, отправленные в виде запроса к веб-API. Тем не менее `recordId` используется для корреляции, поэтому любые записи в ответе с идентификатором `recordId`, который не запрашивался в исходном запросе к веб-API, будут отклонены.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Варианты ошибок
В дополнение к недоступности веб-API или отправке неуспешных кодов состояния случаями ошибок также считаются следующие:

* Если веб-API возвращает код состояния успеха, но ответ указывает, что это не `application/json`, тогда ответ считается недействительным, и никакие обогащения не будут выполнены.
* При наличии **недопустимых** (если `recordId` не находится в исходном запросе или если значения дублируются) записей в массиве `values` ответа **для этих записей** обогащение выполняться не будет.

В тех случаях, когда веб-API недоступен или возвращает ошибку HTTP, в журнал выполнения индексатора будет добавлено понятное сообщение об ошибке с доступными сведениями об ошибке HTTP.

## <a name="see-also"></a>Дополнительные материалы

+ [How to define a skillset](cognitive-search-defining-skillset.md) (Определение набора навыков)
+ [Add custom skill to an AI enrichment pipeline](cognitive-search-custom-skill-interface.md)
+ [Example: Creating a custom skill for AI enrichment](cognitive-search-create-custom-skill-example.md)
