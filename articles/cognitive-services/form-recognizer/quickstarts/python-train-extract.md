---
title: Краткое руководство. Обучение модели и извлечение данных из форм с помощью REST API и Python в Распознавателе документов
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве для обучения модели и извлечения данных из форм будет использоваться REST API Распознавателя документов и Python.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 63b58e5e667d03d7808973b0783ed98ab4cb5447
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605869"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>Краткое руководство. Обучение модели Распознавателя документов и извлечение данных из форм с помощью REST API и Python

В этом кратком руководстве для обучения и оценки документов с последующим извлечением пары "ключ — значение" и таблиц используется REST API и Python в Распознавателе документов Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим кратким руководством требуется следующее:
- Доступ к предварительной версии Распознавателя документов с ограниченным доступом. Чтобы получить доступ к предварительной версии, заполните и отправьте [форму запроса на доступ к Распознавателю документов](https://aka.ms/FormRecognizerRequestAccess).
- Среда [Python](https://www.python.org/downloads/), если вы хотите выполнить этот пример кода локально.
- Минимум пять документов одного типа. Вы будете использовать эти данные для обучения модели. Для работы с этим кратким руководством вы можете использовать [пример набора данных](https://go.microsoft.com/fwlink/?linkid=2090451). Передайте данные в корневой каталог учетной записи хранения хранилища BLOB-объектов Azure.

## <a name="create-a-form-recognizer-resource"></a>Создание ресурса Распознавателя документов

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Обучение модели Распознавателя документов

Для начала вам понадобится набор обучающих данных в контейнере хранилища BLOB-объектов Azure. У вас должны быть как минимум пять заполненных форм (PDF-документы и/или изображения) того же типа и структуры, что и основные входные данные. Или можно использовать одну пустую и две заполненные формы. В имя файла пустой формы необходимо добавить слово "empty". Советы и варианты для объединения данных для обучения см. в статье [Build a training data set for a custom model](../build-training-data-set.md) (Создание обучающего набора данных для пользовательской модели).

Чтобы обучить модель Распознавателя документов с помощью документов в контейнере больших двоичных объектов Azure, вызовите API-интерфейс **обучения**, выполнив код Python, показанный ниже. Перед выполнением команды внесите следующие изменения:

1. Замените `<Endpoint>` URL-адресом конечной точки ресурса Распознавателя документов в том регионе Azure, где вы получили ключи подписки.
1. Замените `<Subscription key>` ключом подписки, скопированным на предыдущем шаге.
1. Замените `<SAS URL>` подписанным URL-адресом контейнера хранилища BLOB-объектов Azure. Чтобы получить подписанный URL-адрес, откройте Обозреватель службы хранилища Microsoft Azure, щелкните контейнер правой кнопкой мыши и выберите **Get shared access signature** (Получить подписанный URL-адрес). Убедитесь, что разрешение на **чтение** и разрешение **списка** установлены и нажмите кнопку **Создать**. Затем скопируйте значение в разделе **URL-адрес**. Оно должно быть в таком формате: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

    ```python
    ########### Python Form Recognizer Train #############
    from requests import post as http_post

    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    source = r"<SAS URL>"
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<Subscription Key>',
    }
    url = base_url + "/train" 
    body = {"source": source}
    try:
        resp = http_post(url = url, json = body, headers = headers)
        print("Response status code: %d" % resp.status_code)
        print("Response body: %s" % resp.json())
    except Exception as e:
        print(str(e))
    ```
1. Сохраните код как файл с расширением .py. Например, *form-recognize-train.py*.
1. Откройте окно командной строки.
1. В командной строке выполните пример кода с помощью команды `python`. Например, `python form-recognize-train.py`.

Вы получите ответ `200 (Success)` со следующими выходными данными JSON:

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

Запишите значение `"modelId"`. Оно понадобится вам позже.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Извлечение таблиц и пар "ключ — значение" из форм

Теперь нужно проанализировать документ и извлечь из него пары "ключ — значение" и таблицы. Вызовите API **Анализ модели**, выполнив следующий скрипт Python. Перед выполнением команды внесите следующие изменения:

1. Замените `<Endpoint>` конечной точкой, полученной из ключа подписки Распознавателя документов (см. ресурс Распознавателя документов на вкладке **Обзор**).
1. Замените `<path to your form>` путем к файлу формы (например, C:\temp\file.pdf).
1. Замените `<modelID>` идентификатором модели, полученным в предыдущем разделе.
1. Замените `<file type>` типом файла Поддерживаемые типы: `application/pdf`, `image/jpeg`, `image/png`.
1. Замените `<subscription key>` ключом своей подписки.

    ```python
    ########### Python Form Recognizer Analyze #############
    from requests import post as http_post
    
    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    file_path = r"<path to your form>"
    model_id = "<modelID>"
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        url = base_url + "/models/" + model_id + "/analyze" 
        with open(file_path, "rb") as f:
            data_bytes = f.read()  
        resp = http_post(url = url, data = data_bytes, headers = headers)
        print("Response status code: %d" % resp.status_code)    
        print("Response body:\n%s" % resp.json())   
    except Exception as e:
        print(str(e))
    ```

1. Сохраните код как файл с расширением .py. Например, *form-recognize-analyze.py*.
1. Откройте окно командной строки.
1. В командной строке выполните пример кода с помощью команды `python`. Например, `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Изучите ответ.

Успешный ответ будет возвращен в формате JSON. Он представляет пары "ключ — значение" и таблицы, извлеченные из формы:

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве для обучения модели и ее запуска в примере используется REST API Распознавателя документов и Python. Для более подробного изучения API Распознавателя документов см. справочную документацию.

> [!div class="nextstepaction"]
> [Справочная документация по REST API](https://aka.ms/form-recognizer/api)
