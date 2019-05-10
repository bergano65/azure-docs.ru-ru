---
title: Краткое руководство. Распознаватель документов. Обучение модели и извлечение данных формы с помощью cURL
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве для обучения модели и извлечения данных из форм вы будете использовать REST API Распознавателя документов с помощью cURL.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.openlocfilehash: 1afe9239dcc3f5a24d2e950ec7b563bf53d1f04c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143239"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-using-rest-api-with-curl"></a>Краткое руководство. Обучение модели Распознавателя документов и извлечение данных форм с помощью REST API и cURL

В этом кратком руководстве описано, как использовать REST API Распознавателя документов с cURL для обучения и оценки форм, а также извлечения пар "ключ — значение" и таблиц.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Доступ к предварительной версии с ограниченным доступом к Распознавателю документов. Чтобы получить доступ к предварительной версии, заполните и отправьте [форму запроса на доступ к Распознавателю документов Cognitive Services](https://aka.ms/FormRecognizerRequestAccess). 
* [cURL](https://curl.haxx.se/windows/).
* У вас должен быть ключ подписки для Распознавателя документов. Следуйте инструкциям из статьи [Создание учетной записи Cognitive Services на портале Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), чтобы оформить подписку на Распознаватель документов и получить ключ.
* У вас должен быть минимальный набор из пяти форм того же типа. Для работы с этим руководством вы можете использовать [пример набора данных](https://go.microsoft.com/fwlink/?linkid=2090451).

## <a name="train-a-form-recognizer-model"></a>Обучение модели Распознавателя документов

Для начала вам потребуется набор обучающих данных. Вы можете использовать данные в BLOB-объекте Azure или свои собственные локальные данные для обучения. У вас должны быть как минимум пять примеров форм (PDF-документы и/или изображения) того же типа и структуры, что и основные входные данные. Вместо этого можно использовать одну пустую форму. Имя файла формы включает в себя слово empty.

Чтобы обучить модель Распознавателя документов с помощью документов в контейнере BLOB-объектов Azure, вызовите программный интерфейс **обучения**, выполнив следующую команду cURL. Перед выполнением команды внесите следующие изменения:

* замените `<Endpoint>` конечной точкой, которую вы получили из своего ключа подписки Распознавателя документов (она находится на вкладке "Общие сведения о ресурсе" в форме Распознавателя документов);
* замените `<SAS URL>` подписанным URL-адресом (SAS) для общего доступа к контейнеру хранилища BLOB-объектов Azure, в котором расположены данные для обучения;  
* Замените `<subscription key>` ключом своей подписки.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Вы получите ответ `200 (Success)` со следующими выходными данными JSON.

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

Запишите значение `"modelId"`. Оно понадобится для выполнения следующих действий.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Извлечение таблиц и пар "ключ — значение" из форм

Далее вы проанализируете документ и извлечете из него пары "ключ — значение" и таблицы. Вызовите API **Анализ модели**, выполнив приведенную ниже команду cURL. Перед выполнением команды внесите следующие изменения:

* замените `<Endpoint>` конечной точкой, которую вы получили из своего ключа подписки Распознавателя документов (см. ресурс Распознавателя документов на вкладке **Обзор**);
* замените `<modelID>` идентификатором модели, который был получен на предыдущем этапе обучения модели;
* замените `<path to your form>` на путь к файлу вашей формы;
* Замените `<subscription key>` ключом своей подписки.
* замените `<file type>` типом файла (поддерживаемые типы: PDF, изображение JPEG, изображение PNG).

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=application/<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Изучите ответ.

Успешный ответ возвращается в формате JSON и представляет собой извлеченные пары "ключ — значение" и таблицы из формы.

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

В этом руководстве вы использовали интерфейсы REST API Распознавателя документов с помощью cURL для обучения модели и запустили их в примере. Для более подробного изучения API Распознавателя документов см. справочную документацию.

> [!div class="nextstepaction"]
> [Справочная документация по REST API](https://aka.ms/form-recognizer/api)
