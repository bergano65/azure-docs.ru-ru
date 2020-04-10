---
title: Копирование данных от Oracle Eloqua (Предварительный просмотр)
description: Узнайте, как копировать данные из Oracle Eloqua в поддерживаемые хранилища данных, используемые в качестве приемников, с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: abb323bbdb2d49f46fea83e1c881f1ef6a4efff7
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990809"
---
# <a name="copy-data-from-oracle-eloqua-using-azure-data-factory-preview"></a>Копирование данных из Oracle Eloqua с помощью фабрики данных Azure (предварительная версия)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Oracle Eloqua. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!IMPORTANT]
> Сейчас этот соединитель доступен в режиме предварительной версии. Попробуйте его и оставьте свой отзыв. Если вы хотите использовать в своем решении зависимость от соединителей в предварительной версии, обратитесь в службу [поддержки Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем Oracle Eloqua поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Данные из Oracle Eloqua можно скопировать в любое поддерживаемое хранилище данных, которое используется в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Oracle Eloqua.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Oracle Eloqua поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Eloqua** | Да |
| endpoint | Конечная точка сервера Eloqua Eloqua поддерживает несколько центров обработки данных. Чтобы определить свою конечную точку, войдите в https://login.eloqua.com со своими учетными данными, а затем скопируйте фрагмент **базового URL-адреса** из URL-адреса перенаправления в следующем формате: `xxx.xxx.eloqua.com`. | Да |
| username | Имя сайта и имя пользователя учетной записи Eloqua в формате `SiteName\Username`, например `Eloqua\Alice`.  | Да |
| password | Пароль, соответствующий имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| useEncryptedEndpoints | Указывает, шифруются ли конечные точки источника данных с помощью протокола HTTPS. Значение по умолчанию — true.  | нет |
| useHostVerification | Уточняется, следует ли требовать, чтобы имя хоста в сертификате сервера соответствовало названию сервера при подключении к TLS. Значение по умолчанию — true.  | нет |
| usePeerVerification | Уточняется, следует ли проверять личность сервера при подключении к TLS. Значение по умолчанию — true.  | нет |

**Примере:**

```json
{
    "name": "EloquaLinkedService",
    "properties": {
        "type": "Eloqua",
        "typeProperties": {
            "endpoint" : "<base URL e.g. xxx.xxx.eloqua.com>",
            "username" : "<site name>\\<user name e.g. Eloqua\\Alice>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных [datasets](concepts-datasets-linked-services.md) для определения наборов данных, см. В этом разделе содержится список свойств, поддерживаемых набором данных Oracle Eloqua.

Чтобы скопировать данные из Oracle Eloqua, задайте для свойства type набора данных значение **EloquaObject**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено: **EloquaObject** | Да |
| tableName | Имя таблицы. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "EloquaDataset",
    "properties": {
        "type": "EloquaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Eloqua linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником Oracle Eloqua.

### <a name="eloqua-as-source"></a>Eloqua в качестве источника

Чтобы копировать данные из Oracle Eloqua, задайте для типа источника в действии копирования значение **EloquaSource**. Следующие свойства поддерживаются в разделе **источника активности** копирования:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **EloquaSource**. | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например: `"SELECT * FROM Accounts"`. | Нет (если для набора данных задано свойство tableName) |

**Примере:**

```json
"activities":[
    {
        "name": "CopyFromEloqua",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Eloqua input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "EloquaSource",
                "query": "SELECT * FROM Accounts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Следующие шаги
См. список хранилищ данных, [поддерживаемых фабрикой данных Azure](copy-activity-overview.md#supported-data-stores-and-formats).
