---
title: Сохранение метаданных и ACL с помощью действия копирования в фабрике данных Azure
description: Узнайте, как сохранить метаданные и списки ACL во время копирования с помощью действия копирования в фабрике данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: jingwang
ms.openlocfilehash: 056909f5fd5838e5ae50fb84bd3535029d862acf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260843"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Сохранение метаданных и ACL с помощью действия копирования в фабрике данных Azure

При использовании действия копирования фабрики данных Azure для копирования данных из источника в приемник в следующих сценариях можно также сохранить метаданные и списки ACL.

## <a name="preserve-metadata"></a>Сохранение метаданных для Lake Migration

При переносе данных из одного Data Lake в другой, включая [Amazon S3](connector-amazon-simple-storage-service.md), [большой двоичный объект Azure](connector-azure-blob-storage.md)и [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md), можно сохранить метаданные файла вместе с данными.

Действие копирования поддерживает сохранение следующих атрибутов во время копирования данных:

- **Все указанные клиентом метаданные** 
- И следующие **пять встроенных системных свойств хранилища данных**: `contentType`, `contentLanguage` (за исключением Amazon S3), `contentEncoding`, `contentDisposition`, `cacheControl`.

При копировании файлов "как есть" из Amazon S3/Azure Data Lake Storage 2-го поколения или большого двоичного объекта Azure в Azure Data Lake Storage 2-го поколения или BLOB-объект Azure с двоичным форматом параметр " **сохранить** " можно найти на вкладке " **действие копирования** " > " **Параметры** " для создания действий или на странице " **Параметры** " в средстве копирование данных.

![Сохранить метаданные действия копирования](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Ниже приведен пример конфигурации JSON действия копирования (см. `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls"></a>Сохранение списков управления доступом из Data Lake Storage 1-го поколения в Gen2

При обновлении с Azure Data Lake Storage 1-го поколения до Gen2 можно сохранить список управления доступом (ACL) POSIX вместе с файлами данных. Дополнительные сведения об управлении доступом см. в разделе [Контроль доступа в Azure Data Lake Storage 1-го поколения](../data-lake-store/data-lake-store-access-control.md) и [управление доступом в Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-access-control.md).

Действие копирования поддерживает сохранение следующих типов списков ACL во время копирования данных. Можно выбрать один или несколько типов:

- **ACL**: копирование и сохранение списков управления доступом POSIX для файлов и каталогов. Он копирует все существующие списки ACL из источника в приемник. 
- **Владелец**: Скопируйте и сохраните пользователя-владельца файлов и каталогов. Требуется доступ суперпользователя к Data Lake Storage 2-го поколения приемника.
- **Группа**: копирование и сохранение группы владельцев файлов и каталогов. Необходим доступ суперпользователя к Data Lake Storage 2-го поколения приемника или пользователю-владельцу (если владелец также является членом Целевой группы).

Если вы укажете копирование из папки, фабрика данных реплицирует списки управления доступом для указанной папки, а также файлы и каталоги, если `recursive` имеет значение true. Если указать копирование из одного файла, то списки ACL для этого файла будут скопированы.

>[!NOTE]
>При использовании ADF для сохранения списков ACL из Data Lake Storage 1-го поколения в Gen2 существующие списки ACL на Gen2's соответствующих папках и файлах будут перезаписаны.

>[!IMPORTANT]
>Если вы решили сохранить списки управления доступом, убедитесь, что вы предоставляли достаточно высокий уровень разрешений для работы фабрики данных с учетной записью Data Lake Storage 2-го поколения приемника. Например, используйте проверку подлинности с помощью ключа учетной записи или назначьте роль владельца данных BLOB-объекта хранилища субъекту-службе или управляемому удостоверению.

При настройке источника как Data Lake Storage 1-го поколения с двоичным форматом или параметром копирования в двоичном формате, а также для приемника как Data Lake Storage 2-го поколения с двоичным форматом или с параметром двоичного копирования, параметр **сохранить** можно найти на странице **Параметры** в копирование данных средстве или на вкладке **Параметры** > **копирования** для создания действий.

![Data Lake Storage 1-го поколения Gen2 сохранить список ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Ниже приведен пример конфигурации JSON действия копирования (см. `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия

См. другие статьи о действиях копирования:

- [Действие копирования в фабрике данных Azure](copy-activity-overview.md)
- [Руководство по настройке производительности действия копирования](copy-activity-performance.md)
