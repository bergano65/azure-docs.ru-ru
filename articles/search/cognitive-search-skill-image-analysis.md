---
title: Когнитивный навык анализа изображений
titleSuffix: Azure Cognitive Search
description: Извлекайте семантический текст с помощью анализа изображений с помощью когнитивных навыков анализа изображений в конвейере обогащения ИИ в Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4ff6972e2f7ea219a1c8c8dbabbf9fe12a8fa59e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369475"
---
# <a name="image-analysis-cognitive-skill"></a>Когнитивный навык анализа изображений

Навык **анализа изображений** извлекает полноценный набор визуальных компонентов на основе содержимого изображения. Например, можно создать заголовок из изображения, сформировать теги, а также определить знаменитостей и ориентиры. Этот навык использует модели машинного обучения, предоставляемые [API компьютерного зрения](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) в Cognitive Services. 

> [!NOTE]
> Небольшие объемы (до 20 транзакций) могут быть выполнены бесплатно в Azure Cognitive Search, но большие рабочие нагрузки требуют [присоединения оплачиваемого ресурса Cognitive Services.](cognitive-search-attach-cognitive-services.md) Плата взимается при вызове API в Cognitive Services и извлечении изображений при распознавании документов в службе "Когнитивный поиск Azure". За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Плата за извлечение изображений указана на [странице с ценами на службу "Когнитивный поиск Azure"](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Имя параметра     | Описание |
|--------------------|-------------|
| defaultLanguageCode   |  Строка, указывающая язык для возврата данных. Служба возвращает результаты распознавания на указанном языке. Если этот параметр не задан, используется значение по умолчанию "en". <br/><br/>Поддерживаемые языки: <br/>*en* — английский (по умолчанию) <br/> *es* - Испанский <br/> *ja* - Японский <br/> *pt* - Португальский <br/> *zh* — китайский (упрощенное письмо)|
| visualFeatures |  Массив строк, указывающих возвращаемые типы визуальных компонентов. Допустимые типы визуальных компонентов:  <ul><li>*взрослый* - обнаруживает, если изображение порнографического характера (изображает наготу или половой акт), или гори (изображает крайнее насилие или кровь). Сексуально наводящий содержание (ака колоритный содержание) также обнаружены.</li><li>*бренды* - обнаруживает различные бренды в изображении, в том числе приблизительное местоположение. Визуальная функция *брендов* доступна только на английском языке.</li><li> *категории* - классифицирует содержимое изображения в соответствии с таксономией, определенной в документации Cognitive Services [Computer Vision.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy) </li><li>*описание* - описывает содержимое изображения с полным предложением на поддерживаемых языках.</li><li>*лица* - обнаруживает, если лица присутствуют. Если присутствует, генерирует координаты, пол и возраст.</li><li> *объекты* - обнаруживает различные объекты в изображении, в том числе приблизительное местоположение. Визуальная функция *объектов* доступна только на английском языке.</li><li> *tags* — помечает изображение подробным списком слов, связанных с содержимым изображения.</li></ul> Имена визуальных компонентов зависят от регистра. Обратите внимание, что *цвет* и *imageType* визуальные функции были обесцвечены, но эта функциональность все еще может быть доступна через [пользовательские навыки.](https://go.microsoft.com/fwlink/?linkid=2121117)|
| подробности   | Массив строк, указывающих возвращаемые сведения о домене. Допустимые типы визуальных компонентов: <ul><li>*знаменитости* - идентифицирует знаменитостей, если обнаружены в изображении.</li><li>*ориентиры* - определяет ориентиры, если обнаружены на изображении. </li></ul> |

## <a name="skill-inputs"></a>Входные данные навыков

| Ввод имени      | Описание                                          |
|---------------|------------------------------------------------------|
| Изображение         | Сложный тип. Сейчас работает только с полем /document/normalized_images, созданным индексатором большого двоичного объекта Azure, если для ```imageAction``` установлено значение, отличное от ```none```. Дополнительные сведения см. в [этом примере](#sample-output).|



##  <a name="sample-skill-definition"></a>Определение навыка образца

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "tags",
                "categories",
                "description",
                "faces",
                "brands"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                },
                {
                    "name": "brands"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>Индекс примеров (только для категорий, описания, лиц и полей тегов)
```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```
### <a name="sample-output-field-mapping-for-the-above-index"></a>Отображение поля выборочного производства (для вышеуказанного индекса)
```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/brands/*/name",
            "targetFieldName": "brands"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>Вариация карт выходного поля (вложенные свойства)

Отображение полей вывода можно определить для свойств более низкого уровня, таких как только ориентиры или знаменитости. В этом случае убедитесь, что в схеме индекса есть поле, в каком именно содержит ориентиры.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```
##  <a name="sample-input"></a>Пример ввода

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
                }
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Пример выходных данных

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "categories": [
          {
            "name": "abstract_",
            "score": 0.00390625
          },
          {
            "name": "people_",
            "score": 0.83984375,
            "detail": {
              "celebrities": [
                {
                  "name": "Satya Nadella",
                  "faceBoundingBox": [
                        {
                            "x": 273,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 431
                        },
                        {
                            "x": 273,
                            "y": 431
                        }
                    ],
                  "confidence": 0.999028444
                }
              ],
              "landmarks": [
                {
                  "name": "Forbidden City",
                  "confidence": 0.9978346
                }
              ]
            }
          }
        ],
        "adult": {
          "isAdultContent": false,
          "isRacyContent": false,
          "isGoryContent": false,
          "adultScore": 0.0934349000453949,
          "racyScore": 0.068613491952419281,
          "goreScore": 0.08928389008070282
        },
        "tags": [
          {
            "name": "person",
            "confidence": 0.98979085683822632
          },
          {
            "name": "man",
            "confidence": 0.94493889808654785
          },
          {
            "name": "outdoor",
            "confidence": 0.938492476940155
          },
          {
            "name": "window",
            "confidence": 0.89513939619064331
          }
        ],
        "description": {
          "tags": [
            "person",
            "man",
            "outdoor",
            "window",
            "glasses"
          ],
          "captions": [
            {
              "text": "Satya Nadella sitting on a bench",
              "confidence": 0.48293603002174407
            }
          ]
        },
        "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
        "metadata": {
          "width": 1500,
          "height": 1000,
          "format": "Jpeg"
        },
        "faces": [
          {
            "age": 44,
            "gender": "Male",
            "faceBoundingBox": [
                {
                    "x": 1601,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 447
                },
                {
                    "x": 1601,
                    "y": 447
                }
            ]
          }
        ],
        "objects": [
          {
            "rectangle": {
              "x": 25,
              "y": 43,
              "w": 172,
              "h": 140
            },
            "object": "person",
            "confidence": 0.931
          }
        ],
        "brands":[  
           {  
              "name":"Microsoft",
              "confidence": 0.903,
              "rectangle":{  
                 "x":20,
                 "y":97,
                 "w":62,
                 "h":52
              }
           }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Варианты ошибок
При указанных ниже ошибках никакие элементы не извлекаются.

| Код ошибки | Описание |
|------------|-------------|
| NotSupportedLanguage | Указанный язык не поддерживается. |
| InvalidImageUrl | URL-адрес изображения имеет неправильный формат или недоступен.|
| InvalidImageFormat | Входные данные не являются допустимым изображением. |
| InvalidImageSize | Входное изображение слишком велико. |
| NotSupportedVisualFeature  | Указанный тип компонента недопустим. |
| NotSupportedImage | Неподдерживаемое изображение, например детская порнография. |
| InvalidDetails | Неподдерживаемая доменная модель. |

Если вы получаете `"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"`ошибку похож на, проверьте путь. И знаменитости и достопримечательности свойства под `detail`.

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>См. также

+ [Встроенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [Создание индексатора — REST](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
