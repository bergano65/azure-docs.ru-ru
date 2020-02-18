---
title: Определение проекций в хранилище знаний
titleSuffix: Azure Cognitive Search
description: Примеры распространенных шаблонов для проецирования дополненных документов в хранилище знаний для использования с Power BI или МАШИНным обучением Azure.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165518"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>Проекции магазина базы знаний: как формировать и экспортировать обогащения в хранилище знаний

> [!IMPORTANT] 
> Сейчас предоставляется общедоступная предварительная версия хранилища знаний. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Версия REST API 2019-05-06-Preview](search-api-preview.md) предоставляет предварительные версии функций. Сейчас доступна ограниченная поддержка портала. Поддержка пакета SDK для .NET пока не реализована.

Проекции — это физическое выражение для обогащенных документов в хранилище знаний. Для эффективного использования расширенных документов требуется структура. В этой статье вы ознакомитесь с структурой и связями, узнаете, как создавать свойства проекции, а также как связывать данные между создаваемыми типами проекций. 

Чтобы создать проекцию, необходимо сформировать данные с помощью навыка для создания пользовательского объекта или использовать синтаксис встроенного формирования. Фигура данных содержит все данные, которые вы собираетесь проецировать. В этом документе приводится пример каждого варианта, в котором можно выбрать один из вариантов для создаваемых проекций.


Существует три типа проекций:
+ Таблицы
+ Объекты
+ Файлы

Проекции таблиц хранятся в хранилище таблиц Azure. Проекции объектов и файлов записываются в хранилище BLOB-объектов. проекции объектов сохраняются как JSON-файлы и могут содержать содержимое из документа, а также любые выводы и дополнения навыков. Конвейер обогащения также может извлекать двоичные файлы, например изображения, эти двоичные файлы проецируются в виде файловых проекций. Если двоичный объект проецируется как проекция объекта, только связанные с ним метаданные сохраняются в виде большого двоичного объекта JSON. 

Чтобы понять, как пересечение между формированием и проекцией данных, мы будем использовать следующий набор навыков для изучения различных конфигураций. Этот набор навыков обрабатывает необработанное изображение и текстовое содержимое. Проекции будут определены на основе содержимого документа и выходных данных навыков для сценариев, которые мы хотим поддерживать.

Кроме того, можно скачать и использовать [пример REST API](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json) со всеми вызовами в этом пошаговом руководстве.

> [!IMPORTANT] 
> При экспериментах с проекциями полезно [задать свойство кэша индексатора](search-howto-incremental-index.md) , чтобы обеспечить управление затратами. Изменение проекций приведет к тому, что весь документ будет дополнен снова, если кэш индексатора не задан. Если кэш установлен и изменения внесены только в проекции, то при выполнении наборов навыков для ранее дополненных документов не взимается плата за Cognitive Services.


```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Теперь можно добавить объект `knowledgeStore` и настроить проекции для каждого из сценариев по мере необходимости. 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>Проецирование на таблицы для таких сценариев, как Power BI

> [!NOTE] 
> Поскольку хранилищем знаний является учетная запись хранения Azure, табличные проекции представляют собой таблицы службы хранилища Azure и управляются ограничениями хранилища таблиц. Дополнительные сведения см. в разделе [ограничения хранилища таблиц](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). Полезно помнить, что размер сущности не может превышать 1 МБ, а одно свойство не может быть больше 64 КБ. Эти ограничения делают таблицы хорошим решением для хранения большого количества небольших сущностей.

Power BI может считывать данные из таблиц и обнаруживать связи на основе ключей, создаваемых проекциями в магазине, это делает таблицы хорошим вариантом для данных проекта при попытке построить панель мониторинга для обогащенных данных. При условии, что мы пытаемся создать панель мониторинга, на которой можно визуализировать ключевые фразы, извлеченные из документов, в виде текстового облака, мы можем добавить к набору навыков навык, чтобы создать пользовательскую фигуру с подробными сведениями о документе и ключевыми фразами. Чтобы создать новое обогащение, именуемое ```pbiShape``` на ```document```, добавьте навык создания фигуры в набор навыков.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Использование навыка фигуры для создания пользовательской фигуры

Создайте пользовательскую фигуру, которую можно проецировать в хранилище таблиц. Без пользовательской фигуры проекция может ссылаться только на один узел (одна проекция на выход). Создание пользовательской фигуры позволяет объединять различные элементы в новое логическое целое число, которое может быть проецировано в виде одной таблицы или разбито по коллекциям таблиц. В этом примере пользовательская фигура сочетает метаданные и идентифицированные сущности и ключевые фразы. Объект называется Пбишапе и является родительским по отношению к `/document`. 

> [!IMPORTANT] 
> Исходные пути для обогащения должны быть правильно сформированными объектами JSON, прежде чем их можно будет прогнозировать. Дерево обогащения может представлять обогащения, которые не являются правильными форматами JSON, например, когда обогащение является родительским для примитве, как строка. Обратите внимание, что `KeyPhrases` и `Entities` упаковываются в допустимый объект JSON с `sourceContext`, это необходимо, так как `keyphrases` и `entities` являются дополнением к примитивам и их необходимо преобразовать в допустимый формат JSON, прежде чем их можно будет запланировать.

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForTables",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "metadata_storage_content_type",
                    "source": "/document/metadata_storage_content_type",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_storage_name",
                    "source": "/document/metadata_storage_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_storage_path",
                    "source": "/document/metadata_storage_path",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_content_type",
                    "source": "/document/metadata_content_type",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyPhrases",
                    "source": null,
                    "sourceContext": "/document/merged_content/keyphrases/*",
                    "inputs": [
                        {
                            "name": "KeyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        }

                    ]
                },
                {
                    "name": "Entities",
                    "source": null,
                    "sourceContext": "/document/merged_content/entities/*",
                    "inputs": [
                        {
                            "name": "Entities",
                            "source": "/document/merged_content/entities/*/name"
                        }

                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "pbiShape"
                }
            ]
        }
```
Добавьте навык, который мы только что определили в список умений в наборе навыков. 

Теперь, когда у нас есть все данные, необходимые для проецирования в таблицы, обновите объект Кновледжесторе с помощью определений таблиц. 

```json

"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Задайте для свойства ```storageConnectionString``` допустимую строку подключения учетной записи хранения общего назначения v2. В этом сценарии мы определяем три таблицы в объекте проекции, устанавливая свойства ```tableName```, ```source``` и ```generatedKeyName```. Теперь можно обновить набор навыков, выполнив запрос на размещение.

### <a name="slicing"></a>Среза 

При работе с консолидированной фигурой, в которой все содержимое, которое необходимо спроецировать, находится в одной фигуре (или на узле обогащения), срезы предоставляют возможность разбить один узел на несколько таблиц или объектов. Здесь объект ```pbiShape``` разбит на несколько таблиц. Функция среза позволяет извлекать части фигуры, ```keyPhrases``` и ```Entities``` в отдельные таблицы. Это полезно, так как несколько сущностей и фраз связаны с каждым документом. При неявном фрагментировании создается связь между родительской и дочерней таблицами с использованием ```generatedKeyName``` в родительской таблице для создания столбца с тем же именем в дочерней таблице. 

### <a name="naming-relationships"></a>Именование связей
Свойства ```generatedKeyName``` и ```referenceKeyName``` используются для связи данных между таблицами или даже между типами проекции. Каждая строка дочерней таблицы или проекции имеет свойство, указывающее на родительский элемент. Имя столбца или свойства дочернего элемента — это ```referenceKeyName``` из родительского элемента. Если ```referenceKeyName``` не предоставлен, служба по умолчанию применяет ее к ```generatedKeyName``` из родительского объекта. PowerBI использует эти созданные ключи для обнаружения связей в таблицах. Если столбец в дочерней таблице необходим по-другому, установите свойство ```referenceKeyName``` в родительской таблице. Одним из примеров может быть установка ```generatedKeyName``` в качестве идентификатора в таблице Пбидокумент, а ```referenceKeyName``` как DocumentID. Это приведет к тому, что столбец в таблицах Пбиентитиес и Пбикэйфрасес, содержащий идентификатор документа, называется DocumentID.

Сохраните обновленный набор навыков и запустите индексатор. Теперь у вас есть рабочая проекция с тремя таблицами. Импорт этих таблиц в Power BI должен привести к Power BI автоматическому обнаружению связей.

## <a name="projecting-to-objects"></a>Проецирование на объекты

Проекции объектов не имеют тех же ограничений, что и проекции таблиц, лучше подходят для проецирования больших документов. В этом примере мы выполним проецирование всего документа в проекцию объекта. Проекции объектов ограничиваются одной проекцией в контейнере.
Чтобы определить проекцию объекта, мы будем использовать массив ```objects``` в проекциях. Вы можете создать новую фигуру с помощью навыка для фигуры или использовать встроенную форму проекции объекта. Хотя в примере таблиц показан подход к созданию фигуры и среза, в этом примере демонстрируется использование встроенной формы. Встроенная форма — это возможность создать новую фигуру в определении входных данных для проекции. Встроенная форма создает анонимный объект, идентичный тому, что будет создавать подобный элемент. Встроенная форма полезна при определении фигуры, которую не планируется использовать повторно.
Свойство проекций — это массив. для этого примера мы добавим новый экземпляр проекция в массив. Обновление определения Кновледжесторе с помощью встроенных проекций, при использовании встроенных проекций не требуется навыка для фигуры.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```
## <a name="file-projections"></a>Проекции файлов

Проектные проекции файлов — это изображения, которые либо извлекаются из исходного документа, либо из результатов обогащения, которые могут быть выделены из процесса обогащения. Проекции файлов, схожие с проекциями объектов, реализуются как большие двоичные объекты и содержат изображение. Чтобы создать файловую проекцию, мы используем массив ```files``` в объекте проекции. В этом примере проецируется все изображения, извлеченные из документа, в контейнер с именем `samplefile`.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Проецирование на несколько типов

Более сложный сценарий может потребовать наличия содержимого проекта в разных типах проекций. Например, если необходимо проецировать некоторые данные, такие как ключевые фразы и сущности, в таблицы, сохраняйте результаты распознавания текста и макета в виде объектов и проецировать изображения в виде файлов. Это обновление для набора навыков будет следующим:

1. Создайте таблицу со строкой для каждого документа.
2. Создайте таблицу, связанную с таблицей документов, с каждой ключевой фразой, идентифицированной в виде строки в этой таблице.
3. Создайте таблицу, связанную с таблицей документов, с каждой сущностью, идентифицированной в виде строки в этой таблице.
4. Создайте проекцию объекта с текстом макета для каждого изображения.
5. Создание проекции файла, проецирование каждого извлеченного изображения.
6. Создайте таблицу перекрестных ссылок, содержащую ссылки на таблицу документов, проекцию объекта с текстом макета и проекцию файла.

Начните с добавления нового навыка в массив навыков, который создает объект-форму. 

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForCross",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "metadata_storage_name",
                    "source": "/document/metadata_storage_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyPhrases",
                    "source": null,
                    "sourceContext": "/document/merged_content/keyphrases/*",
                    "inputs": [
                        {
                            "name": "KeyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        }

                    ]
                },
                {
                    "name": "entities",
                    "source": null,
                    "sourceContext": "/document/merged_content/entities/*",
                    "inputs": [
                        {
                            "name": "Entities",
                            "source": "/document/merged_content/entities/*/name"
                        }

                    ]
                },
                {
                    "name": "images",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*",
                    "inputs": [
                        {
                            "name": "image",
                            "source": "/document/normalized_images/*"
                        },
                        {
                            "name": "layoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        },
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                        ]
                }
                
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "crossProjection"
                }
            ]
        }
```

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

Для проецирования объектов требуется имя контейнера для каждой проекции, проекции объектов или проекции файлов не могут совместно использовать контейнер. 

### <a name="relationships"></a>Связи

В этом примере также выделяется еще одна функция проекций, определяющая несколько типов проекций в одном объекте проекции. связь выражена внутри и между различными типами (таблицами, объектами, файлами) проекций, что позволяет чтобы начать со строки таблицы для документа и найти весь текст для изображений в этом документе в проекции объекта. Если вы не хотите, чтобы данные были связаны, определите проекции в различных объектах проекции, например следующий фрагмент приведет к тому, что таблицы будут связаны, но нет связей между таблицами и проекциями текста OCR. Группы проекций полезны в тех случаях, когда требуется проецировать одни и те же данные в разные фигуры для различных нужд. Например, Группа проекции для панели мониторинга Power BI и другая группа проекции для использования данных для обучения модели AI для навыка.
При построении проекций разных типов сначала создаются проекции файлов и объектов, а в таблицы добавляются пути.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

В этих примерах демонстрируются распространенные закономерности использования проекций. Теперь у вас должно быть хорошее понимание концепций, связанных с созданием проекций для конкретного сценария.

## <a name="common-issues"></a>Распространенные проблемы

При определении проекции существует несколько распространенных проблем, которые могут привести к непредвиденным результатам.

1. Не формирование обогащений строк. Если строки расширены, например ```merged_content``` дополненные ключевыми фразами, обогащенное свойство представляется как дочерний элемент merged_content в дереве обогащения. Но во время проекции это необходимо преобразовать в допустимый объект JSON с именем и значением.
2. Пропуск ```/*``` в конце исходного пути. Если, например, источником проекции является ```/document/pbiShape/keyPhrases``` массив ключевых фраз проецируется как один объект или строка. Если задать для исходного пути значение ```/document/pbiShape/keyPhrases/*``` то для каждой из ключевых фраз будет выдаваться одна строка или объект.
3. Селекторы пути чувствительны к регистру и могут привести к отсутствующим входным предупреждениям, если не используется точный регистр для выбора.

