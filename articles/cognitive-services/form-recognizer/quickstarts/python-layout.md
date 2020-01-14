---
title: Краткое руководство. Извлечение текста и сведений о макете с помощью Python — Распознаватель документов
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве для чтения текста и данных таблиц из форм будет использоваться REST API макета Распознавателя документов и Python.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: pafarley
ms.openlocfilehash: ea716caf575fce1197c12f3cb3cea8348c7a04c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451004"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Краткое руководство. Извлечение текста и сведений о макете с использованием Распознавателя документов и Python

В этом кратком руководстве вы используете REST API Распознавателя документов Azure и Python для извлечения сведений о макете текста и данных таблиц из документов формы.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительные требования

Для работы с этим кратким руководством требуется следующее:
- Доступ к предварительной версии Распознавателя документов с ограниченным доступом. Чтобы получить доступ к предварительной версии, заполните и отправьте [форму запроса на доступ к Распознавателю документов](https://aka.ms/FormRecognizerRequestAccess).
- Среда [Python](https://www.python.org/downloads/), если вы хотите выполнить этот пример кода локально.
- Документ формы. Вы можете скачать образ из [примера набора данных](https://go.microsoft.com/fwlink/?linkid=2090451) для этого краткого руководства.

## <a name="create-a-form-recognizer-resource"></a>Создание ресурса Распознавателя документов

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Анализ макета формы

Для начала анализа макета запустите API **[анализа макета](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** , используя приведенный ниже скрипт Python. Перед выполнением сценария внесите следующие изменения:

1. Замените `<Endpoint>` конечной точкой, полученной из подписки Распознавателя документов.
1. Замените `<path to your form>` на путь к документу локальной формы.
1. Замените `<subscription key>` ключом подписки, скопированным на предыдущем шаге.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Сохраните код как файл с расширением .py. Например, *form-recognizer-layout.py*.
1. Откройте окно командной строки.
1. В командной строке выполните пример кода с помощью команды `python`. Например, `python form-recognizer-layout.py`.

Вы получите ответ, включающий заголовок `202 (Success)`**Operation-Location**, который сценарий выведет в окно консоли. Этот заголовок содержит идентификатор операции, который можно использовать для запроса состояния асинхронной операции и получения результатов. В следующем примере значения строка после `operations/` является идентификатором операции.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>Получение результатов макета

После вызова API **анализа макета** вызовите API **[получения результатов анализа макета](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** , чтобы получить состояние операции и извлеченных данных. Добавьте следующий код в нижнюю часть сценария Python. При этом в новом вызове API используется значение идентификатора операции. Этот сценарий вызывает API с регулярными интервалами, пока не станут доступны результаты. Мы рекомендуем установить интервал одну секунду или более.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
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
1. Снова используйте команду `python` для выполнения примера. Например, `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Изучите ответ.

Скрипт выведет ответы в консоль до завершения операции **анализа макета**. Затем извлеченные данные будут выведены в формате JSON. Узел `"readResults"` содержит каждую строку текста с соответствующим расположением ограничивающего прямоугольника на странице. В поле `"pageResults"` отображаются все фрагменты текста в таблицах с координатами (строка и столбец).

Ознакомьтесь со следующим изображением накладной и его соответствующими выходными данными в формате JSON. Выходные данные сокращены для простоты.

> [!div class="mx-imgBorder"]
> ![Документ счета Contoso с таблицей](../media/contoso-invoice.png)

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-11-12T19:55:36Z",
  "lastUpdatedDateTime":"2019-11-12T19:55:43Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "language":"en",
        "angle":0,
        "width":8.5,
        "height":11,
        "unit":"inch",
        "lines":[ 
          { 
            "language":"en",
            "boundingBox":[ 
              0.5384,
              1.1583,
              1.4466,
              1.1583,
              1.4466,
              1.3534,
              0.5384,
              1.3534
            ],
            "text":"Contoso",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.5384,
                  1.1583,
                  1.4466,
                  1.1583,
                  1.4466,
                  1.3534,
                  0.5384,
                  1.3534
                ],
                "text":"Contoso",
                "confidence":1
              }
            ]
          },
          { 
            "language":"en",
            "boundingBox":[ 
              0.7994,
              1.5143,
              1.3836,
              1.5143,
              1.3836,
              1.6154,
              0.7994,
              1.6154
            ],
            "text":"Address:",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.7994,
                  1.5143,
                  1.3836,
                  1.5143,
                  1.3836,
                  1.6154,
                  0.7994,
                  1.6154
                ],
                "text":"Address:",
                "confidence":1
              }
            ]
          },
          ...
          { 
            "language":"en",
            "boundingBox":[ 
              6.2285,
              3.4114,
              6.3919,
              3.4114,
              6.3919,
              3.5119,
              6.2285,
              3.5119
            ],
            "text":"PT",
            "words":[ 
              { 
                "boundingBox":[ 
                  6.2285,
                  3.4114,
                  6.3919,
                  3.4114,
                  6.3919,
                  3.5119,
                  6.2285,
                  3.5119
                ],
                "text":"PT",
                "confidence":1
              }
            ]
          }
        ]
      }
    ],
    "pageResults":[ 
      { 
        "page":1,
        "tables":[ 
          { 
            "rows":2,
            "columns":6,
            "cells":[ 
              { 
                "rowIndex":0,
                "columnIndex":0,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5075,
                  2.8088,
                  1.9061,
                  2.8088,
                  1.9061,
                  3.3219,
                  0.5075,
                  3.3219
                ],
                "elements":[ 
                  "#/readResults/0/lines/8/words/0",
                  "#/readResults/0/lines/8/words/1"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве для извлечения макета текста накладной вы использовали REST API Распознавателя документов и Python. Для более подробного изучения API Распознавателя документов см. справочную документацию.

> [!div class="nextstepaction"]
> [Справочная документация по REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)
