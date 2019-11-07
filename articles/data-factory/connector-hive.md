---
title: Копирование данных из Hive с помощью фабрики данных Azure
description: Узнайте, как копировать данные из Hive в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных Azure.
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
ms.openlocfilehash: 6bc644c960fdfa24c7ae7e190d5a110cdba41f9c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680850"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Копирование данных из Hive с помощью фабрики данных Azure 

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Hive. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Hive поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из Hive можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Hive.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Hive поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Hive**. | Да |
| host | IP-адрес или имя узла сервера Hive, разделенные точкой с запятой ";" для нескольких узлов (только если включен параметр Сервицедисковеримоде).  | Да |
| порт | TCP-порт, используемый сервером Hive для прослушивания клиентских подключений. При подключении к Azure HDInsights укажите порт 443. | Да |
| serverType | Тип сервера Hive. <br/>Допустимые значения: **HiveServer1**, **HiveServer2**, **HiveThriftServer**. | Нет |
| thriftTransportProtocol | Транспортный протокол для использования в слое Thrift. <br/>Допустимые значения: **Binary**, **SASL**, **HTTP** | Нет |
| authenticationType | Метод проверки подлинности, используемый для доступа к серверу Hive. <br/>Допустимые значения: **Anonymous**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService**. | Да |
| serviceDiscoveryMode | Значение true, если служба ZooKeeper используется, значение false — если нет.  | Нет |
| zooKeeperNameSpace | Пространство имен на ZooKeeper, в которое добавлены 2 узла сервера Hive.  | Нет |
| useNativeQuery | Указывает, использует ли драйвер собственные запросы HiveQL или преобразует их в эквивалентную форму в HiveQL.  | Нет |
| Имя пользователя | Имя пользователя, которое позволяет получить доступ к серверу Hive.  | Нет |
| пароль | Пароль, соответствующий пользователю. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Нет |
| httpPath | Частичный URL-адрес, соответствующий серверу Hive.  | Нет |
| enableSsl | Указывает, шифруются ли подключения к серверу с помощью протокола SSL. По умолчанию для этого параметра используется значение false.  | Нет |
| trustedCertPath | Полный путь к PEM-файлу, который содержит сертификаты доверенного ЦС для проверки сервера при подключении по протоколу SSL. Это свойство может устанавливаться только при использовании SSL в локальных средах выполнения интеграции. Значением по умолчанию является файл cacerts.pem, который устанавливается вместе с IR.  | Нет |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС из доверенного хранилища системы или из указанного PEM-файла. По умолчанию для этого параметра используется значение false.  | Нет |
| allowHostNameCNMismatch | Указывает, следует ли требовать, чтобы имя SSL-сертификата, выданного ЦС, совпадало с именем узла сервера при подключении по протоколу SSL. По умолчанию для этого параметра используется значение false.  | Нет |
| allowSelfSignedServerCert | Указывает, следует ли разрешить использование самозаверяющих сертификатов с сервера. По умолчанию для этого параметра используется значение false.  | Нет |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

**Пример**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел содержит список свойств, поддерживаемых набором данных Hive.

Чтобы скопировать данные из Hive, установите свойство типа набора данных **HiveObject**. Поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство Type набора данных должно иметь значение **хивеобжект** . | Да |
| schema | Имя схемы. |Нет (если свойство query указано в источнике действия)  |
| таблица | Имя таблицы. |Нет (если свойство query указано в источнике действия)  |
| tableName | Имя таблицы, включая часть схемы. Это свойство поддерживается для обеспечения обратной совместимости. Для новой рабочей нагрузки используйте `schema` и `table`. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником Hive.

### <a name="hivesource-as-source"></a>HiveSource в качестве источника

Чтобы копировать данные из Hive, установите тип источника **HiveSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **HiveSource**. | Да |
| запрос | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Пример**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
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


## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
