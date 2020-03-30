---
title: Копирование данных из источников доступа Майкрософт
description: Узнайте, как копировать данные из источников Microsoft Access в поддерживаемые хранилища данных раковины с помощью действия копирования в конвейере Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: d6110065e28b8f179cd1d113107fb3508e1c3e44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892545"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Копирование данных из хранилищ данных Microsoft Access с помощью Azure Data Factory

В этой статье излагается, как использовать деятельность копирования в Azure Data Factory для копирования данных из хранилища данных Microsoft Access. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем Microsoft Access поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Вы можете скопировать данные из источника Microsoft Access в любой поддерживаемый хранилище данных раковины. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать этот разъем Microsoft Access, необходимо:

- Настроить локальную среду выполнения интеграции. Дополнительные сведения см. в статье [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md).
- Установите драйвер Microsoft Access ODBC для хранилища данных на машине Интеграции Runtime.

>[!NOTE]
>Microsoft Access 2016 версия драйвера ODBC не работает с этим разъемом. Вместо этого используйте версию драйвера 2013 или 2010.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах приводится подробная информация о свойствах, используемых для определения сущностей Data Factory, специфичных для разъема Microsoft Access.

## <a name="linked-service-properties"></a>Свойства связанной службы

Следующие свойства поддерживаются для службы Microsoft Access, связанной с доступом:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа должно быть установлено на: **MicrosoftAccess** | Да |
| connectionString | Строка соединения ODBC, исключая часть учетных данных. Можно указать строку соединения или использовать систему DSN (Имя источника данных), настроенную на машине Integration Runtime (необходимо по-прежнему указывать часть учетных данных в связанной службе соответственно).<br> Вы также можете поместить пароль в Хранилище `password` ключей Azure и вытащить конфигурацию из строки соединения.С более подробной информацией обратитесь к учетным данным Хранилища в Хранилище  [ключей Azure.](store-credentials-in-key-vault.md)| Да |
| authenticationType | Тип аутентификации, используемый для подключения к хранилику данных Microsoft Access.<br/>Разрешенные значения: **Основные** и **Анонимные**. | Да |
| userName | При использовании обычной проверки подлинности укажите имя пользователя. | нет |
| password | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | нет |
| учетные данные | Учетные данные в строке подключения, используемые для получения доступа и указанные в формате "драйвер-определенное свойство-значение". Пометьте это поле в качестве SecureString. | нет |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Требуется локальная среда IR, как упоминалось в разделе [Предварительные требования](#prerequisites). |Да |

**Примере:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Полный список разделов и свойств, доступных [datasets](concepts-datasets-linked-services.md) для определения наборов данных, см. В этом разделе приводится список свойств, поддерживаемых набором данных Microsoft Access.

Для копирования данных из Microsoft Access поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено на: **MicrosoftAccessTable** | Да |
| tableName | Название таблицы в Microsoft Access. | Нет для источника (если свойство query указано в источнике действия).<br/>Да для приемника. |

**Пример**

```json
{
    "name": "MicrosoftAccessDataset",
    "properties": {
        "type": "MicrosoftAccessTable",
        "linkedServiceName": {
            "referenceName": "<Microsoft Access linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе приводится список свойств, поддерживаемых источником доступа Майкрософт.

### <a name="microsoft-access-as-source"></a>Доступ к Microsoft как источник

Для копирования данных из хранилища данных, совместимых с microsoft Access, в разделе **источника активности** копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования должно быть установлено: **MicrosoftAccessSource** | Да |
| query | Используйте пользовательский запрос для чтения данных. Например: `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Примере:**

```json
"activities":[
    {
        "name": "CopyFromMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Microsoft Access input dataset name>",
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
                "type": "MicrosoftAccessSource",
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

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
