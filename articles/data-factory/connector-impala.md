---
title: Копирование данных из Impala с помощью Фабрики данных Azure
description: Узнайте, как копировать данные из Impala в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 3c7690390936a05dd472796eb6f50f582f652e35
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990854"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Копирование данных из Impala с помощью Фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Impala. Это продолжение [статьи с обзором действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем Impala поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Данные из Impala можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилив данных, которые поддерживаются в качестве источников или появляются в потоке активности копирования, см. [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats)

В фабрике данных предоставляется встроенный драйвер, который обеспечивает подключение. Поэтому не нужно вручную устанавливать драйвер для использования этого соединителя.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Impala.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Impala поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа должно быть установлено на **Impala.** | Да |
| host | IP-адрес или имя узла сервера Impala (192.168.222.160).  | Да |
| порт | TCP-порт, используемый сервером Impala для прослушивания клиентских подключений. Значение по умолчанию — 21050.  | нет |
| authenticationType | Тип проверки подлинности. <br/>Допустимые значения: **Anonymous**, **SASLUsername** и **UsernameAndPassword**. | Да |
| username | Имя пользователя, используемое для доступа к серверу Impala. Значение по умолчанию является анонимным при использовании SASLUsername.  | нет |
| password | Пароль, который соответствует имени пользователя при использовании UsernameAndPassword. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | нет |
| enableSsl | Уточняется, шифруются ли соединения на сервер с помощью TLS. По умолчанию используется значение **false**.  | нет |
| trustedCertPath | Полный путь файла .pem, содержащего доверенные сертификаты CA, используемые для проверки сервера при подключении к TLS. Это свойство может быть установлено только при использовании TLS на самостоятельном запуске интеграции. Значением по умолчанию является файл cacerts.pem, который устанавливается вместе со средой выполнения интеграции.  | нет |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС из доверенного хранилища системы или из указанного PEM-файла. По умолчанию используется значение **false**.  | нет |
| allowHostNameCNMismatch | Уточняется, следует ли требовать, чтобы при подключении к TLS-TLS/SSL было названо сертификат TLS/SSL, выданное CA. По умолчанию используется значение **false**.  | нет |
| allowSelfSignedServerCert | Указывает, следует ли разрешить использование самозаверяющих сертификатов с сервера. По умолчанию используется значение **false**.  | нет |
| connectVia | [Время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Узнайте больше из [раздела Prerequisites.](#prerequisites) Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

**Примере:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Impala.

Чтобы скопировать данные из Impala, установите свойство type набора данных **ImpalaObject**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено на: **ImpalaObject** | Да |
| схема | Имя схемы. |Нет (если свойство query указано в источнике действия)  |
| table | Имя таблицы. |Нет (если свойство query указано в источнике действия)  |
| tableName | Название стола со схемой. Это свойство поддерживается для обратной совместимости. Использовать `schema` `table` и для новой рабочей нагрузки. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых типом источника Impala.

### <a name="impala-as-a-source-type"></a>Impala в качестве источника данных

Чтобы копировать данные из Impala, установите тип источника **ImpalaSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type источника действия копирования необходимо задать значение **ImpalaSource**. | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Примере:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
                "query": "SELECT * FROM MyTable"
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
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
