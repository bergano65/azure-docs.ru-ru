---
title: Копирование данных из Phoenix с помощью фабрики данных Azure
description: Узнайте, как копировать данные из Phoenix в поддерживаемые хранилища данных в качестве приемников с помощью действия копирования в конвейере фабрики данных Azure.
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
ms.openlocfilehash: 128946562fb3976bcb98de6c0911b7f89805b149
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680430"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Копирование данных из Phoenix с помощью фабрики данных Azure 

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Phoenix. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Phoenix поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из Phoenix можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Phoenix.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Phoenix поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Phoenix**. | Да |
| host | IP-адрес или имя узла сервера Phoenix (это 192.168.222.160).  | Да |
| порт | TCP-порт, используемый сервером Phoenix для прослушивания клиентских подключений. Значение по умолчанию — 8765. При подключении к Azure HDInsights укажите порт 443. | Нет |
| httpPath | Частичный URL-адрес, соответствующий серверу Phoenix (то есть /gateway/sandbox/phoenix/version). Укажите `/hbasephoenix0` при использовании кластера HDInsights.  | Нет |
| authenticationType | Механизм аутентификации, используемый для подключения к серверу Phoenix. <br/>Допустимые значения: **Anonymous**, **UsernameAndPassword**, **WindowsAzureHDInsightService**. | Да |
| Имя пользователя | Имя пользователя, используемое для подключения к серверу Phoenix.  | Нет |
| пароль | Пароль, соответствующий имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Нет |
| enableSsl | Указывает, шифруются ли подключения к серверу с помощью протокола SSL. По умолчанию для этого параметра используется значение false.  | Нет |
| trustedCertPath | Полный путь к PEM-файлу, который содержит сертификаты доверенного ЦС для проверки сервера при подключении по протоколу SSL. Это свойство может устанавливаться только при использовании SSL в локальных средах выполнения интеграции. Значением по умолчанию является файл cacerts.pem, который устанавливается вместе с IR.  | Нет |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС из доверенного хранилища системы или из указанного PEM-файла. По умолчанию для этого параметра используется значение false.  | Нет |
| allowHostNameCNMismatch | Указывает, следует ли требовать, чтобы имя SSL-сертификата, выданного ЦС, совпадало с именем узла сервера при подключении по протоколу SSL. По умолчанию для этого параметра используется значение false.  | Нет |
| allowSelfSignedServerCert | Указывает, следует ли разрешить использование самозаверяющих сертификатов с сервера. По умолчанию для этого параметра используется значение false.  | Нет |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

>[!NOTE]
>Если кластер не поддерживает прикрепление сеанса, например HDInsight, явным образом добавьте индекс узла в конце параметра пути http, например, укажите `/hbasephoenix0` вместо `/hbasephoenix`.

**Пример**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbasephoenix0",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Phoenix.

Чтобы копировать данные из Phoenix, установите свойство type набора данных **PhoenixObject**. Поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство Type набора данных должно иметь значение **фоениксобжект** . | Да |
| schema | Имя схемы. |Нет (если свойство query указано в источнике действия)  |
| таблица | Имя таблицы. |Нет (если свойство query указано в источнике действия)  |
| tableName | Имя таблицы со схемой. Это свойство поддерживается для обеспечения обратной совместимости. Используйте `schema` и `table` для новой рабочей нагрузки. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником Phoenix.

### <a name="phoenix-as-source"></a>Phoenix в качестве источника

Чтобы скопировать данные из Phoenix, задайте тип источника **PhoenixSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **PhoenixSource**. | Да |
| запрос | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Пример**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
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
