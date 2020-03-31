---
title: Краткое руководство. Распознаватель документов. Обучение модели и извлечение данных формы с помощью cURL
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве для обучения модели и извлечения данных из форм используется REST API Распознавателя документов с помощью cURL.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 32756187852de0834afc1dc034d3f7419f0c8087
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118389"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Краткое руководство. Обучение модели Распознавателя документов и извлечение данных форм с помощью REST API и cURL

В этом кратком руководстве для обучения и оценки документов с последующим извлечением пары "ключ — значение" и таблиц используется REST API и cURL в Распознавателе документов Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством требуется следующее:
- Установленная программа [cURL](https://curl.haxx.se/windows/).
- Минимум шесть документов одного типа. Вы будете использовать пять из них для обучения модели, а затем протестируйте ее с помощью шестой формы. У форм могут быть разные типы файлов, но типы документов должны быть одинаковыми. Для работы с этим кратким руководством вы можете использовать [пример набора данных](https://go.microsoft.com/fwlink/?linkid=2090451). Передайте файлы для обучения в корневой каталог контейнера хранилища BLOB-объектов в учетной записи хранения Azure. Файлы тестирования можно разместить в отдельной папке.

## <a name="create-a-form-recognizer-resource"></a>Создание ресурса Распознавателя документов

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Обучение модели Распознавателя документов

Для начала вам понадобится набор обучающих данных в хранилище BLOB-объектов Azure. У вас должны быть как минимум пять заполненных форм (PDF-документы и/или изображения) того же типа и структуры, что и основные входные данные. Или можно использовать одну пустую и две заполненные формы. В имя файла пустой формы необходимо добавить слово "empty". Советы и варианты для объединения данных для обучения см. в статье [Build a training data set for a custom model](../build-training-data-set.md) (Создание обучающего набора данных для пользовательской модели).

> [!NOTE]
> Используя функцию помеченных данных, можно заранее пометить вручную некоторые или все данные для обучения. Это более сложный процесс, но его результатом является лучшая обученная модель. Дополнительные сведения об этой функции см. в разделе об [обучении с использованием меток](../overview.md#train-with-labels).

Чтобы обучить модель Распознавателя документов с помощью документов в контейнере больших двоичных объектов Azure, вызовите API-интерфейс **[обучения пользовательской модели](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** , выполнив команду cURL, показанную ниже. Перед выполнением команды внесите следующие изменения:

1. Замените `<Endpoint>` конечной точкой, полученной из подписки Распознавателя документов.
1. Замените `<subscription key>` ключом подписки, скопированным на предыдущем шаге.
1. Замените `<SAS URL>` подписанным URL-адресом контейнера хранилища BLOB-объектов Azure. Чтобы получить подписанный URL-адрес, откройте Обозреватель службы хранилища Microsoft Azure, щелкните контейнер правой кнопкой мыши и выберите **Get shared access signature** (Получить подписанный URL-адрес). Убедитесь, что разрешение на **чтение** и разрешение **списка** установлены и нажмите кнопку **Создать**. Затем скопируйте значение в разделе **URL-адрес**. Оно должно быть в таком формате: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Вы получите ответ `201 (Success)` с заголовком **Location**. Значение этого заголовка — это идентификатор новой обучаемой модели. 

## <a name="get-training-results"></a>Получение результатов обучения

После начала операции обучения используется новая операция, **[Get Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)** (Получение пользовательской модели), для проверки состояния обучения. Передайте идентификатор модели в этот вызов API, чтобы проверить состояние обучения:

1. Замените `<Endpoint>` конечной точкой, полученной из ключа подписки Распознавателя документов
1. Замените `<subscription key>` ключом своей подписки.
1. Замените `<model ID>` идентификатором модели, полученным на предыдущем шаге.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Вы получите ответ `200 (Success)` с текстом JSON в следующем формате. Обратите внимание на поле `"status"`. После завершения обучения оно будет иметь значение `"ready"`. Если обучение модели не завершено, необходимо повторить запрос к службе, повторно выполнив команду. Мы рекомендуем установить между вызовами интервал в одну секунду или более.

Поле `"modelId"` содержит идентификатор обучаемой модели. Он потребуется для выполнения следующего шага.

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Анализ форм для пар "ключ — значение" и таблиц

Теперь с помощью новой обученной модели нужно проанализировать документ и извлечь из него пары "ключ — значение" и таблицы. Вызовите API **[анализа формы](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** , выполнив следующую команду. Перед выполнением команды внесите следующие изменения:

1. Замените `<Endpoint>` конечной точкой, которую вы получили из своего ключа подписки Распознавателя документов (см. ресурс Распознавателя документов на вкладке **Обзор**).
1. Замените `<model ID>` идентификатором модели, полученным в предыдущем разделе.
1. Замените `<SAS URL>` URL-адресом SAS для файла в службе хранилища Azure. Выполните действия, описанные в разделе "Обучение", но вместо получения URL-адреса SAS для всего контейнера больших двоичных объектов, получите его для определенного файла, который нужно проанализировать.
1. Замените `<subscription key>` ключом своей подписки.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Вы получите ответ `202 (Success)`, содержащий заголовок **Operation-Location**. Значение этого заголовка содержит идентификатор результатов, используемый для отслеживания результатов операции анализа. Сохраните этот идентификатор результатов для следующего шага.

## <a name="get-the-analyze-results"></a>Получение результатов анализа

Выполните запрос результатов операции анализа с использованием следующего API-интерфейса.

1. Замените `<Endpoint>` конечной точкой, которую вы получили из своего ключа подписки Распознавателя документов (см. ресурс Распознавателя документов на вкладке **Обзор**).
1. Замените `<result ID>` идентификатором, полученным в предыдущем разделе.
1. Замените `<subscription key>` ключом своей подписки.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Вы получите ответ `200 (Success)` с текстом JSON в следующем формате. Выходные данные сокращены для простоты. Обратите внимание на поле `"status"` внизу. По завершении операции анализа оно будет иметь значение `"succeeded"`. Если операция анализа не завершена, необходимо повторить запрос к службе, повторно выполнив команду. Мы рекомендуем установить между вызовами интервал в одну секунду или более.

Основные связи пар "ключ — значение" и таблицы находятся в узле `"pageResults"`. Если вы также указали извлечение простого текста с помощью параметра URL-адреса *includeTextDetails*, то на узле `"readResults"` будет отображаться содержимое и позиции всего текста в документе.

```json
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Улучшение результатов

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Дальнейшие действия

В примере, приведенном в этом руководстве, для обучения модели и ее запуска используется REST API Распознавателя документов и cURL. Для более подробного изучения API Распознавателя документов см. справочную документацию.

> [!div class="nextstepaction"]
> [Справочная документация по REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
