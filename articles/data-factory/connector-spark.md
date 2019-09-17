---
title: Копирование данных из Spark с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из Spark в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: bbd751592facc1a6194e052a486de15cace774cd
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010364"
---
# <a name="copy-data-from-spark-using-azure-data-factory"></a>Копирование данных из Spark с помощью фабрики данных Azure 

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Spark. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Spark поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой исходной матрицей](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из Spark можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, относящихся к соединителю Spark.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Spark поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойству type необходимо задать следующее значение: **Spark** | Да |
| host | IP-адрес или имя узла сервера Spark  | Да |
| port | TCP-порт, используемый сервером Spark для прослушивания клиентских подключений. При подключении к Azure HDInsights укажите порт 443. | Да |
| serverType | Тип сервера Spark. <br/>Допустимые значения: **SharkServer**, **SharkServer2**, **SparkThriftServer** | Нет |
| thriftTransportProtocol | Транспортный протокол для использования в слое Thrift. <br/>Допустимые значения: **Binary**, **SASL**, **HTTP** | Нет |
| authenticationType | Метод аутентификации, используемый для доступа к серверу Spark. <br/>Допустимые значения: **Anonymous**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Да |
| username | Имя пользователя, которое позволяет получить доступ к серверу Spark.  | Нет |
| password | Пароль, соответствующий пользователю. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Нет |
| httpPath | Частичный URL-адрес, соответствующий серверу Spark.  | Нет |
| enableSsl | Указывает, шифруются ли подключения к серверу с помощью протокола SSL. По умолчанию для этого параметра используется значение false.  | Нет |
| trustedCertPath | Полный путь к PEM-файлу, который содержит сертификаты доверенного ЦС для проверки сервера при подключении по протоколу SSL. Это свойство может устанавливаться только при использовании SSL в локальных средах выполнения интеграции. Значением по умолчанию является файл cacerts.pem, который устанавливается вместе с IR.  | Нет |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС из доверенного хранилища системы или из указанного PEM-файла. По умолчанию для этого параметра используется значение false.  | Нет |
| allowHostNameCNMismatch | Указывает, следует ли требовать, чтобы имя SSL-сертификата, выданного ЦС, совпадало с именем узла сервера при подключении по протоколу SSL. По умолчанию для этого параметра используется значение false.  | Нет |
| allowSelfSignedServerCert | Указывает, следует ли разрешить использование самозаверяющих сертификатов с сервера. По умолчанию для этого параметра используется значение false.  | Нет |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

**Пример.**

```json
{
    "name": "SparkLinkedService",
    "properties": {
        "type": "Spark",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Spark.

Чтобы скопировать данные с Spark, установите свойство type набора данных **SparkObject**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type набора данных необходимо задать следующее значение: **SparkObject** | Да |
| schema | Имя схемы. |Нет (если свойство query указано в источнике действия)  |
| table | Имя таблицы. |Нет (если свойство query указано в источнике действия)  |
| tableName | Имя таблицы со схемой. Это свойство поддерживается для обеспечения обратной совместимости. Используйте `schema` и`table` для новой рабочей нагрузки. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "SparkDataset",
    "properties": {
        "type": "SparkObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Spark linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником Spark.

### <a name="spark-as-source"></a>Spark в качестве источника

Чтобы скопировать данные из Spark, установите тип источника **SparkSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь следующее значение: **SparkSource** | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSpark",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Spark input dataset name>",
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
                "type": "SparkSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Следующие шаги
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
