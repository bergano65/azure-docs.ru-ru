---
title: Краткое руководство. Извлечение данных квитанции с помощью cURL в Распознавателе документов
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве для извлечения данных с изображений квитанции будет использоваться REST API Распознавателя документов и cURL.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: 0178e53e6a7fde54b988e710a1cabbb7ded69b22
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592574"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Краткое руководство. Извлечение данных квитанции с помощью REST API Распознавателя документов и cURL

В этом кратком руководстве для извлечения и определения соответствующей информации из квитанций будет использоваться REST API Распознавателя документов Azure и cURL.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим кратким руководством требуется следующее:
- Доступ к предварительной версии Распознавателя документов с ограниченным доступом. Чтобы получить доступ к предварительной версии, заполните и отправьте [форму запроса на доступ к Распознавателю документов](https://aka.ms/FormRecognizerRequestAccess).
- Установленная программа [cURL](https://curl.haxx.se/windows/).
- URL-адрес изображения квитанции. Вы можете использовать [пример изображения](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) при изучении этого краткого руководства.

## <a name="create-a-form-recognizer-resource"></a>Создание ресурса Распознавателя документов

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Анализ квитанции

Для начала анализа квитанции запустите API **анализа**, используя приведенную ниже команду cURL. Перед выполнением команды внесите следующие изменения:

1. Замените `<Endpoint>` конечной точкой, которую вы получили из своего ключа подписки Распознавателя документов (см. ресурс Распознавателя документов на вкладке **Обзор**).
1. Замените `<your receipt URL>` на URL-адрес изображения квитанции.
1. Замените `<subscription key>` ключом подписки, скопированным на предыдущем шаге.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

Вы получите ответ `202 (Success)`, содержащий заголовок **Operation-Location**. Значение этого заголовка содержит идентификатор операции, который можно использовать для запроса статуса операции и получения результатов. В следующем примере строка после `operations/` является идентификатором операции.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Получение результатов анализа данных квитанции

После вызова API **анализа квитанции** вызовите API **получения результатов анализа квитанции**, чтобы узнать о статусе операции и извлеченных данных.

1. Замените `<operationId>` идентификатором операции из предыдущего шага.
1. Замените `<subscription key>` ключом своей подписки.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Изучите ответ.

Вы получите ответ `200 (Success)` со следующими выходными данными JSON. В первом поле, `"status"`, указывается состояние операции. Если операция завершена, поле `"recognitionResults"` будет содержать каждую строку текста, извлеченного из квитанции, а поле `"understandingResults"` будет содержать сведения пары "ключ — значение" для самых подходящих элементов квитанции. Если операция не завершена, значение поля `"status"` будет `"Running"` или `"NotStarted"`, и вам снова понадобится вызвать API вручную или с помощью сценария. Мы рекомендуем установить между вызовами интервал в одну секунду или более.

Ознакомьтесь со следующим изображением квитанции и его соответствующими выходными данными в формате JSON. Выходные данные сокращены для удобства чтения.

![Квитанция из магазина Contoso](../media/contoso-receipt.png)

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве для извлечения содержания с изображений квитанции используется REST API Распознавателя документов и cURL. Для более подробного изучения API Распознавателя документов см. справочную документацию.

> [!div class="nextstepaction"]
> [Справочная документация по REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
