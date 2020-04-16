---
title: Сохранение метаданных и ACL с помощью копирования на фабрике данных Azure
description: Узнайте о том, как сохранить метаданные и АКЛ во время копирования с помощью копирования на фабрике данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: jingwang
ms.openlocfilehash: 5ce1b85394a7bb604841f7fb941bdebf12c0bca2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414150"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Сохранение метаданных и ACL с помощью копирования на фабрике данных Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

При использовании активности копирования Azure Data Factory для копирования данных из источника в раковину в следующих сценариях можно также сохранить метаданные и АКЛ.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a>Сохранение метаданных для миграции озер

При переносе данных из одного озера данных в другое, включая [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob](connector-azure-blob-storage.md)и [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md)вы можете сохранить метаданные файлов вместе с данными.

Активность копирования поддерживает сохранение следующих атрибутов во время копирования данных:

- **Все указанные клиентом метаданные** 
- И следующие **пять данных хранения встроенных системных свойств** `contentEncoding`: `contentDisposition` `cacheControl`, `contentType` `contentLanguage` (кроме Amazon S3), , .

При копировании файлов как-то из Amazon S3/Azure Data Lake Storage Gen2/Azure Blob в Azure Data Lake Storage Gen2/Azure Blob с бинарным форматом можно найти **опцию «Сохранение»** на вкладке **«Настройки** **активности копирования»** > для авторства действий или **страницы настроек** в инструменте копирования данных.

![Копирование активности сохранения метаданных](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Вот пример конфигурации копирования JSON `preserve`(см.): 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
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

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>Сохранение ACLs от хранения данных озера Gen1/Gen2 до Gen2

При обновлении с Azure Data Lake Storage Gen1 до Gen2 или копировании данных между ADLS Gen2 можно сохранить списки управления доступом POSIX (ACLs) вместе с файлами данных. Для получения дополнительной информации о контроле доступа см. [Контроль доступа в Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) и [control Access в Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-access-control.md)

Копирование активности поддерживает сохранение следующих типов ACL во время копирования данных. Вы можете выбрать один или несколько типов:

- **ACL**: Копирование и сохранение списков управления доступом POSIX в файлах и каталогах. Он копирует полные существующие ACLs от источника до раковины. 
- **Владелец**: Копировать и сохранять владельца пользователя файлов и каталогов. Супер-пользователь доступ к раковине Data Lake Storage Gen2 не требуется.
- **Группа**: Копирование и сохранение группы файлов и каталогов. Требуется сверхпользовательский доступ к sink Data Lake Storage Gen2 или пользователю-владелецу (если пользователь-владелец также является членом целевой группы).

Если вы укажете скопировать из папки, Data Factory воспроизводит ACLs для этой `recursive` папки, а также файлы и каталоги под ней, если она установлена на достоверное. Если указать копирование из одного файла, ACLs в этом файле копируются.

>[!NOTE]
>При использовании ADF для сохранения ACLs от хранения данных Lake Storage Gen1/Gen2 до Gen2 существующие ACL на соответствующей папке/файлах Gen2 будут перезаписаны.

>[!IMPORTANT]
>При выборе сохранения АКЛ убедитесь, что вы предоставляете достаточно высокие разрешения для Data Factory для работы против вашей учетной записи Data Lake Storage Gen2. Например, используйте аутентификацию ключа учетной записи или присвоите роль владельца данных Storage Blob главному или управляемому удостоверению.

При настройке источника в виде Data Lake Storage Gen1/Gen2 с бинарным форматом или опцией бинарной копии и раковиной в виде Data Lake Storage Gen2 с бинарным форматом или опцией бинарной копии можно найти **опцию «Сохранение»** на странице **«Настройки»** в инструменте copy Data Tool или на вкладке **«Настройки** **активности копирования»** > для нараставания.

![Хранение данных озера Gen1/Gen2 для Gen2 Сохранить ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Вот пример конфигурации копирования JSON `preserve`(см.): 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
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
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
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

- [Общие сведения о действии копирования](copy-activity-overview.md)
- [Производительность действия копирования](copy-activity-performance.md)
