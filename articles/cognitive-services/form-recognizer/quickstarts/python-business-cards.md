---
title: Краткое руководство. Извлечение данных визитной карточки с помощью Python — Распознаватель документов
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как использовать REST API Распознавателя документов и Python для извлечения данных с изображений визитных карточек.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 5e27aaebc015f47e0fcdb5da81770d49b86ad000
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934333"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Краткое руководство. Извлечение данных визитной карточки с помощью REST API Распознавателя документов и Python

В этом кратком руководстве описано, как использовать REST API Распознавателя документов Azure и Python для извлечения соответствующей информации из визитных карточек и ее идентификации.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством требуется следующее:
- Среда [Python](https://www.python.org/downloads/), если вы хотите выполнить этот пример кода локально.
- Изображение визитной карточки. Вы можете использовать [пример изображения](../media/business-card-english.jpg) при изучении этого краткого руководства.

> [!NOTE]
> В этом кратком руководстве предполагается использование локального файла. Сведения о получении удаленного изображения визитной карточки по URL-адресу см. в [справочной документации](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync).

## <a name="create-a-form-recognizer-resource"></a>Создание ресурса Распознавателя документов

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Анализ визитной карточки

Для начала анализа визитной карточки запустите API **[анализа визитных карточек](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)** , используя приведенный ниже скрипт Python. Перед выполнением сценария внесите следующие изменения:

1. Замените `<endpoint>` конечной точкой, полученной из подписки Распознавателя документов.
1. Измените `<path to your business card>` на локальный путь к изображению визитной карточки или PDF-файлу.
1. Замените `<subscription key>` ключом подписки, скопированным на предыдущем шаге.
1. Измените `<file type>` на image/jpeg, image/png, application/pdf или image/tiff.

    ```python
    ########### Python Form Recognizer Async Business Cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyze"
    source = r"<path to your business card>"
    content_type = "<file type>"
    
    headers = {
        # Request headers
        'Content-Type': content_type,
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True  # True to output all recognized text
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Сохраните код как файл с расширением .py. Например, *form-recognizer-businesscards.py*.
1. Откройте окно командной строки.
1. В командной строке выполните пример кода с помощью команды `python`. Например, `python form-recognizer-businesscards.py`.

Вы получите ответ, включающий заголовок `202 (Success)`**Operation-Location**, который сценарий выведет в окно консоли. Этот заголовок содержит идентификатор результата, который можно использовать для запрашивания состояния длительной операции и получения результатов. В следующем примере значения строка после `operations/` является идентификатором результата.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>Получение результатов анализа визитной карточки

После вызова API **анализа визитных карточек** вызовите API **[получения результатов анализа визитных карточек](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeBusinessCardResult)** , чтобы узнать о состоянии операции и извлеченных данных. Добавьте следующий код в нижнюю часть сценария Python. При этом в новом вызове API используется значение идентификатора результата. Этот сценарий вызывает API с регулярными интервалами, пока не станут доступны результаты. Мы рекомендуем установить интервал одну секунду или более.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Сохраните сценарий.
1. Снова используйте команду `python` для выполнения примера. Например, `python form-recognizer-businesscards.py`.

### <a name="examine-the-response"></a>Изучите ответ.
![Визитная карточка компании Contoso](../media/business-card-english.jpg)

В этом примере показаны выходные данные JSON, возвращаемые Распознавателем документов. Они усечены для удобочитаемости.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Сценарий выведет ответы в консоль до завершения операции **анализа визитной карточки**. Узел `"readResults"` содержит весь распознанный текст. Текст упорядочивается по страницам, затем по строкам, а затем по отдельным словам. Узел `"documentResults"` содержит обнаруженные моделью значения для конкретной визитной карточки. Здесь вы найдете полезные контактные данные, такие как имя компании, ФИО, номер телефона и т. д.


## <a name="next-steps"></a>Дальнейшие шаги

Из этого краткого руководства вы узнали, как использовать REST API Распознавателя документов и Python для извлечения содержимого из визитной карточки. Для более подробного изучения API Распознавателя документов см. справочную документацию.

> [!div class="nextstepaction"]
> [Справочная документация по REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
