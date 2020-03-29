---
title: Определение проекций в хранилище знаний
titleSuffix: Azure Cognitive Search
description: Примеры общих шаблонов о том, как проецировать обогащенные документы в хранилище знаний для использования с Power BI или Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943684"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>Прогнозы хранения знаний: Как формировать и экспортировать обогащение

> [!IMPORTANT] 
> Сейчас предоставляется общедоступная предварительная версия хранилища знаний. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Версия REST API 2019-05-06-Preview](search-api-preview.md) предоставляет предварительные версии функций. Сейчас доступна ограниченная поддержка портала. Поддержка пакета SDK для .NET пока не реализована.

Проекции являются физическим выражением обогащенных документов в хранилище знаний. Эффективное использование обогащенных документов требует структуры. В этой статье вы изучите как структуру, так и отношения, научитесь создавать свойства проекции, а также как соотнести данные по типам проекций, которые вы создаете. 

Чтобы создать проекцию, необходимо формировать данные, используя либо [навык Shaper](cognitive-search-skill-shaper.md) для создания пользовательского объекта, либо использовать синтаксис в составе синтаксиса в проекции. 

Форма данных содержит все данные, которые вы собираетесь проецировать, сформированные как иерархия узлов. В этой статье показано несколько методов формирования данных, чтобы их можно было проецировать на физические структуры, способствующие отчетности, анализу или обработке ниже по течению. 

Примеры, приведенные в этой статье, можно найти в этом [примере REST API,](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)который вы можете загрузить и запустить в клиенте HTTP.

## <a name="introduction-to-the-examples"></a>Введение в примеры

Если вы знакомы с [прогнозами,](knowledge-store-projection-overview.md)вы помните, что существует три типа:

+ Таблицы
+ Объекты
+ Файлы

Прогнозы таблиц хранятся в хранилище таблицы Azure. Проекции объектов и файлов записываются на хранение капли, где проекции объектов сохраняются как файлы JSON, и могут содержать содержимое исходного документа, а также любые выводы навыков или обогащения. Конвейер обогащения может также извлекать бинарные файлы, такие как изображения, эти бинарные файлы проецируются как файловые проекции. Когда двоичный объект проецируется как проекция объекта, только связанные с ним метаданные сохраняются как капля JSON. 

Чтобы понять пересечение между формированием данных и проекциями, мы будем использовать следующий набор навыков в качестве основы для изучения различных конфигураций. Этот набор навыков обрабатывает необработанное изображение и содержание текста. Прогнозы будут определяться на содержании документа и результатах навыков, для сценариев, которые мы хотим поддержать.

> [!IMPORTANT] 
> При экспериментировании с прогнозами полезно [установить свойство кэша индекса для](search-howto-incremental-index.md) обеспечения контроля затрат. Редактирование прогнозов приведет к тому, что весь документ снова обогатится, если кэш индекса не установлен. Когда кэш установлен и обновлены только проекции, выполнение наборов навыков для ранее обогащенных документов не приводит к новым расходам на Когнитивные Услуги.

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

Используя этот набор навыков, с его нулевым `knowledgeStore` в `knowledgeStore` качестве основы, наш первый пример заполняет объект, настроенный с проекциями, которые создают табулярные структуры данных, которые мы можем использовать в других сценариях. 

## <a name="projecting-to-tables"></a>Проектирование к таблицам

Проектирование таблиц в Хранилище Azure полезно для отчетности и анализа с помощью таких инструментов, как Power BI. Power BI может читать со таблиц и открывать отношения на основе ключей, которые генерируются во время проекции. Если вы пытаетесь создать панель мониторинга, наличие соответствующих данных упростит эту задачу. 

Предположим, что мы пытаемся создать панель мониторинга, где мы можем визуализировать ключевые фразы, извлеченные из документов, как облако слов. Чтобы создать правильную структуру данных, мы можем добавить навык Shaper в набор навыков, чтобы создать пользовательскую форму, которая содержит конкретные детали документа и ключевые фразы. Пользовательская форма будет `pbiShape` вызвана на корневой `document` узла.

> [!NOTE] 
> Таблица прогнозов — таблицы хранения Azure, регулируемые ограничениями на хранение, налагаемыми Хранилищем Azure. Для получения дополнительной [информации см.](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model) Полезно знать, что размер сущности не может превышать 1 МБ, а одно свойство может быть не больше 64 КБ. Эти ограничения делают таблицы хорошим решением для хранения большого количества мелких сущностей.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Использование навыка Shaper для создания пользовательской формы

Создайте пользовательскую форму, которую можно проецировать на хранение таблиц. Без пользовательской формы проекция может ссылаться только на один узлы (одна проекция на выход). Создание пользовательской формы позволяет объединить различные элементы в новое логическое целое, которое можно проецировать как единую таблицу или нарезать и распределить по коллекции таблиц. 

В этом примере пользовательская форма сочетает в себе метаданные и идентифицированные сущности и ключевые фразы. Объект вызывается `pbiShape` и воспитывается `/document`под . 

> [!IMPORTANT] 
> Одной из целей формирования является обеспечение того, чтобы все узлы обогащения выражались в хорошо сформированном JSON, который необходим для проецирования в хранилище знаний. Это особенно верно, когда дерево обогащения содержит узлы, которые не являются хорошо сформированными JSON (например, когда обогащение пригорана к примитиву, как строка).
>
> Обратите внимание на последние два узла, `KeyPhrases` и `Entities`. Они завернуты в действительный объект `sourceContext`JSON с . Это необходимо `keyphrases` как `entities` и обогащение на примитивы и должны быть преобразованы в действительный JSON, прежде чем они могут быть проецированы.
>


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

Добавьте вышеупомянутое мастерство Shaper в набор навыков. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Теперь, когда у нас есть все данные, необходимые для проецировать на таблицы, обновите объект knowledgeStore с определениями таблицы. В этом примере у нас есть `tableName`три `source` `generatedKeyName` таблицы, определяемые настройкой и свойствами.

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

Вы можете обработать свою работу, выполнив следующие следующие действия:

1. Установите ```storageConnectionString``` свойство в действительную строку подключения к учетной записи общего назначения V2 общего назначения.  

1. Обновление набора навыков путем выдачи запроса PUT.

1. После обновления набора навыков запустите индексер. 

Теперь у вас есть рабочая проекция с тремя таблицами. Импорт этих таблиц в Power BI должен привести к автоматическому обнаружению отношений Power BI.

Прежде чем перейти к следующему примеру, давайте пересмотреть аспекты проекции таблицы, чтобы понять механику нарезки и соотвенений данных.

### <a name="slicing"></a>Нарезки 

Нарезка — это метод, который разделяет целую консолидированную форму на составные части. Результат состоит из отдельных, но связанных таблиц, с которыми можно работать индивидуально.

В примере `pbiShape` это консолидированная форма (или узло обогащения). В определении `pbiShape` проекции, нарезается на дополнительные таблицы, что ```keyPhrases``` ```Entities```позволяет вытащить части формы, и . В Power BI это полезно, так как с каждым документом связаны несколько сущностей и ключевые фразы, и вы получите больше информации, если увидите сущности и ключевые фразы как классифицированные данные.

Нарезка неявно генерирует связь между родительскими и ```generatedKeyName``` родительскими таблицами, используя в родительской таблице для создания столбца с тем же именем в таблице ребенка. 

### <a name="naming-relationships"></a>Именование отношений

Свойства ```generatedKeyName``` ```referenceKeyName``` и свойства используются для соотнеса данных по таблицам или даже по типам проекций. Каждая строка в таблице/проекции ребенка имеет свойство, указывающее на родителей. Название столбца или свойства в ребенке от ```referenceKeyName``` родителя. Когда ```referenceKeyName``` услуга не предоставлена, она по ```generatedKeyName``` умолчанию по умолчанию от родителя. 

Power BI опирается на эти генерируемые ключи, чтобы обнаружить отношения в таблицах. Если вам нужна колонка в детском столе, ```referenceKeyName``` названная по-разному, установите свойство на родительском столе. Одним из примеров ```generatedKeyName``` может быть установка идентификатора на таблице pbiDocument и ```referenceKeyName``` как DocumentID. Это приведет к тому, что столбец в таблицах pbiKeyEntities и pbiKeyPhrases, содержащий идентификатор документа, будет называться DocumentID.

## <a name="projecting-to-objects"></a>Проектирование объектов

Прогнозы объектов не имеют тех же ограничений, что и прогнозы таблиц, и лучше подходят для проецирования больших документов. В этом примере мы проецируем весь документ на проекцию объекта. Проекции объектов ограничиваются одной проекцией в контейнере и не могут быть нарезаны.

Чтобы определить проекцию объекта, ```objects``` мы будем использовать массив в проекциях. Вы можете создать новую форму, используя навык Shaper или использовать внее формирование проекции объекта. В то время как пример таблиц продемонстрировал подход к созданию формы и нарезки, этот пример демонстрирует использование вневого формирования. 

Внестройное формирование — это способность создавать новую форму в определении входов в проекцию. Внестройное формирование создает анонимный объект, идентичный тому, что `pbiShape`будет производить навык Shaper (в нашем случае). Влиневое формирование полезно, если вы определяете форму, которую не планируете повторно использовать.

Свойство проекций представляет собой массив. В этом примере мы добавляем новый экземпляр проекции в массив, где определение knowledgeStore содержит встроенные проекции. При использовании влинейных проекций можно опустить навык Shaper.

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

## <a name="projecting-to-file"></a>Проектирование файла

Файловые проекции представляют изображения, которые либо извлекаются из исходного документа, либо выходы обогащения, которые могут быть проецированы из процесса обогащения. Файловые проекции, похожие на проекции объектов, реализуются как капли в Хранилище Azure и содержат изображение. 

Для создания проекции файла `files` мы используем массив в объекте проекции. Этот пример проецирует все изображения, извлеченные из документа, в контейнер под названием `samplefile`.

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

## <a name="projecting-to-multiple-types"></a>Проектирование к нескольким типам

Более сложный сценарий может потребовать проецирования содержимого по типам проекций. Например, если вам нужно проецировать некоторые данные, такие как ключевые фразы и сущности, в таблицы, сохраните результаты OCR текста текста и макета в виде объектов, а затем проецируйте изображения в виде файлов. 

В этом примере обновления набора навыков включают следующие изменения:

1. Создайте таблицу со строкой для каждого документа.
1. Создайте таблицу, связанную с таблицей документов, каждая ключевая фраза определена как строка в этой таблице.
1. Создайте таблицу, связанную с таблицей документов, каждая сущность, идентифицированная как строка в этой таблице.
1. Создайте проекцию объекта с текстом макета для каждого изображения.
1. Создайте проекцию файла, проецируя каждое извлеченное изображение.
1. Создайте перекрестную справочную таблицу, содержащую ссылки на таблицу документов, проекцию объекта с текстом макета и проекцию файла.

Эти изменения отражены в определении knowledgeStore далее вниз. 

### <a name="shape-data-for-cross-projection"></a>Формировать данные для перекрестной проекции

Чтобы получить нужные нам формы для этих проекций, начните с добавления `crossProjection`нового навыка Shaper, который создает образной объект под названием . 

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

### <a name="define-table-object-and-file-projections"></a>Определение прогнозов таблицы, объектов и файлов

С консолидированного объекта crossProjection мы можем разделить объект на несколько таблиц, захватить выход OCR как капли, а затем сохранить изображение в виде файлов (также в хранилище Blob).

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

Проекции объектов требуют названия контейнера для каждой проекции, проекции объектов или файловые проекции не могут совместно гостевых. 

### <a name="relationships-among-table-object-and-file-projections"></a>Отношения между проекциями таблицы, объекта и файла

В этом примере также выделяется еще одна особенность проекций. Определяя несколько типов проекций в одном объекте проекции, существует взаимосвязь, выраженная внутри и между различными типами (таблицы, объекты, файлы), что позволяет начать со строки таблицы для документа и найти весь текст OCR для изображений в рамках этого документа в проекции объекта. 

Если вы не хотите, чтобы данные были связаны между собой, определите проекции в различных объектах проекции. Например, следующий фрагмент приведет к тому, что таблицы будут связаны между собой, но без взаимосвязи между таблицами и прогнозами объекта (текст OCR). 

Группы проекций полезны, когда требуется проецировать одни и те же данные в разных формах для различных нужд. Например, группа проекций для панели мониторинга Power BI и другая группа проекций для сбора данных, используемых для обучения модели машинного обучения, завернутые в пользовательские навыки.

При построении проекций различных типов сначала генерируются проекции файлов и объектов, а пути добавляются к таблицам.

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

## <a name="common-issues"></a>Распространенные проблемы

При определении проекции существует несколько общих проблем, которые могут привести к непредвиденным результатам. Проверьте наличие этих проблем, если выход в хранилище знаний не то, что вы ожидаете.

+ Не формирование обогащения строки в действительный JSON. Когда строки обогащаются, например, `merged_content` обогащены ключевыми фразами, `merged_content` обогащенное свойство представлено как дитя внутри дерева обогащения. Представление по умолчанию не является хорошо сформированным JSON. Поэтому во время проекции убедитесь, что для преобразования обогащения в действительный объект JSON с именем и значением.

+ Опуская ```/*``` в конце исходного пути. Если источником проекции `/document/pbiShape/keyPhrases`является, то массив ключевых фраз проецируется как один объект/строка. Вместо этого установите `/document/pbiShape/keyPhrases/*` исходный путь для получения одной строки или объекта для каждой из ключевых фраз.

+ Ошибки синтаксиса пути. Селекторы путей чувствительны к случаям и могут привести к отсутствующим предупреждениям о входе, если вы не используете точный случай для селектора.

## <a name="next-steps"></a>Дальнейшие действия

Примеры этой статьи демонстрируют общие шаблоны создания проекций. Теперь, когда у вас есть хорошее понимание концепций, вы лучше оснащены для создания прогнозов для вашего конкретного сценария.

Изучая новые возможности, рассмотрите поэтапное обогащение в качестве следующего шага. Инкрементное обогащение основано на кэшировании, что позволяет повторно использовать любые обогащения, которые в противном случае не зависят от изменения навыков. Это особенно полезно для конвейеров, которые включают OCR и анализ изображений.

> [!div class="nextstepaction"]
> [Введение в инкрементное обогащение и кэширование](cognitive-search-incremental-indexing-conceptual.md)

Для обзора прогнозов, узнайте больше о возможностях, таких как группы и нарезки, и как вы [определяете их в наборе навыков](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Прогнозы в хранилище знаний](knowledge-store-projection-overview.md)

