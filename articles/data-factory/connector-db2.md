---
title: Копирование данных из DB2 с помощью Azure Data Factory
description: Узнайте, как копировать данные из DB2 в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: jingwang
ms.openlocfilehash: 22ecac12e049e58e533cdde0078f4a25f6bb2aa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77423833"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Копирование данных из DB2 с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-onprem-db2-connector.md)
> * [Текущая версия](connector-db2.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных DB2. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем базы данных DB2 поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Данные из базы данных DB2 можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель DB2 поддерживает приведенные ниже платформы и версии IBM DB2 с архитектурой распределенной реляционной базы данных (DRDA) SQL Access Manager (SQLAM) версии 9, 10 и 11:

* IBM DB2 для z/OS 12.1
* IBM DB2 для z/OS версии 11.1
* IBM DB2 для z/OS версии 10.1
* IBM DB2 для i версии 7.3
* IBM DB2 для i версии 7.2
* IBM DB2 для i версии 7.1
* IBM DB2 для LUW версии 11
* IBM DB2 для LUW версии 10.5
* IBM DB2 для LUW версии 10.1

>[!TIP]
>Разъем DB2 построен поверх поставщика Microsoft OLE DB для DB2. Чтобы устранить ошибки разъема DB2, обратитесь к [кодам ошибки поставщика данных.](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors)

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Среда выполнения интеграции предоставляет встроенный драйвер DB2, поэтому при копировании данных из DB2 вам не потребуется устанавливать драйвер вручную.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, относящихся к соединителю DB2.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы DB2 поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства типа необходимо задать значение **Db2** | Да |
| server |Имя сервера DB2. Вы можете указать номер порта следом за именем сервера, разделив их двоеточием, например `server:port`. |Да |
| База данных |Имя базы данных DB2. |Да |
| authenticationType |Тип проверки подлинности, используемый для подключения к базе данных DB2.<br/>Допустимое значение: **Базовый**. |Да |
| username |Укажите имя пользователя для подключения к базе данных DB2. |Да |
| password |Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| packageCollection | Укажите, где нужные пакеты автоматически создаются ADF при запросе базы данных. | нет |
| сертификатCommonName | При использовании шифрования Secure Sockets Layer (SSL) или Transport Layer Security (TLS) необходимо ввести значение для общего имени Сертификата. | нет |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Узнайте больше из [раздела Prerequisites.](#prerequisites) Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

> [!TIP]
> Если вы получили сообщение `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`об ошибке, в которое говорится, причина в том, что необходимый пакет не создан для пользователя. По умолчанию ADF попытается создать пакет под коллекцией, названный в качестве пользователя, который вы использовали для подключения к DB2. Укажите свойство сбора пакетов, чтобы указать, где вы хотите, чтобы ADF создал необходимые пакеты при запросе базы данных.

**Примере:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
            "username": "<username>",
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

Полный список разделов и свойств, доступных [datasets](concepts-datasets-linked-services.md) для определения наборов данных, см. Этот раздел содержит список свойств, поддерживаемых набором данных DB2.

Для копирования данных из DB2 поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено на: **Db2Table** | Да |
| схема | Имя схемы. |Нет (если свойство query указано в источнике действия)  |
| table | Имя таблицы. |Нет (если свойство query указано в источнике действия)  |
| tableName | Название стола со схемой. Это свойство поддерживается для обратной совместимости. Использовать `schema` `table` и для новой рабочей нагрузки. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Если вы `RelationalTable` использовали набор набранных данных, он по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый в будущем.

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником DB2.

### <a name="db2-as-source"></a>DB2 в качестве источника

Для копирования данных из DB2 в разделе **источника активности** копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования должно быть установлено на: **Db2Источник** | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Нет (если для набора данных задано свойство tableName) |

**Примере:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Если вы `RelationalSource` использовали набранный источник, он по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый в будущем.

## <a name="data-type-mapping-for-db2"></a>Сопоставление типов данных для DB2

При копировании данных из DB2 используются следующие сопоставления из типов данных DB2 с промежуточными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип базы данных DB2 | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| BigInt |Int64 |
| Двоичные данные |Byte[] |
| BLOB-объект |Byte[] |
| CHAR |Строка |
| Clob |Строка |
| Дата |Datetime |
| DB2DynArray |Строка |
| DbClob |Строка |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| GRAPHIC |Строка |
| Целое число |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Строка |
| LongVarGraphic |Строка |
| Числовой |Decimal |
| Real |Один |
| SmallInt |Int16 |
| Time |TimeSpan |
| Отметка времени |Дата и время |
| VarBinary |Byte[] |
| VarChar |Строка |
| VarGraphic |Строка |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
