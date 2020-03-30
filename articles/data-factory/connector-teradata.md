---
title: Копирование данных с Teradata Vantage с помощью фабрики данных Azure
description: Стерадата Разъем службы Data Factory позволяет копировать данные из Teradata Vantage в хранилища данных, поддерживаемые Data Factory в качестве раковин.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: c7c6cebf0a5c6371893dff52b2e8d7c064a40084
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257943"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Копирование данных с Teradata Vantage с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
>
> * [Версия 1](v1/data-factory-onprem-teradata-connector.md)
> * [Текущая версия](connector-teradata.md)

В этой статье излагается, как использовать активность копирования на фабрике данных Azure для копирования данных с Teradata Vantage. Он основывается на [обзоре активности копирования.](copy-activity-overview.md)

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем Teradata поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Вы можете скопировать данные с Teradata Vantage в любой поддерживаемый хранилище данных раковины. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель Teradata поддерживает:

- Версия Teradata **14.10, 15.0, 15.10, 16.0, 16.10 и 16.20**.
- Копирование данных с помощью **проверки** подлинности Basic или **Windows.**
- Параллельное копирование из источника Teradata. Для получения подробной информации из [раздела Teradata смотрите параллельную копию раздела Teradata.](#parallel-copy-from-teradata)

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Если вы используете self-hosted Интеграция Runtime, обратите внимание, что он обеспечивает встроенный драйвер Teradata, начиная с версии 3.18. Вам не нужно вручную устанавливать драйвер. Драйвер требует "Visual C е Redistributable 2012 Обновление 4" на самостоятельной интеграции запуска машины. Если вы еще не установили его, скачать его [здесь](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах приводится подробная информация о свойствах, используемых для определения сущностей Data Factory, специфичных для разъема Teradata.

## <a name="linked-service-properties"></a>Свойства связанной службы

Служба, связанная с Teradata, поддерживает следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа должно быть установлено на **Teradata.** | Да |
| connectionString | Определяет информацию, необходимую для подключения к экземпляру Teradata. Ознакомьтесь с приведенными ниже примерами.<br/>Вы также можете поместить пароль в Хранилище ключей Azure и вытащить конфигурацию `password` из строки соединения. С более подробной информацией обратитесь к [учетным данным Хранилища в Хранилище ключей Azure.](store-credentials-in-key-vault.md) | Да |
| username | Укажите имя пользователя для подключения к Teradata. Применяется при использовании аутентификации Windows. | нет |
| password | Укажите пароль для учетной записи пользователя, указанный для имени пользователя. Вы также можете ссылаться [на секрет, хранящийся в Хранилище ключей Azure.](store-credentials-in-key-vault.md) <br>Применяется при использовании аутентификации Windows или ссылки на пароль в Key Vault для базовой аутентификации. | нет |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Узнайте больше из [раздела Prerequisites.](#prerequisites) Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

Дополнительные свойства соединения, которые можно установить в строке соединения в вашем случае:

| Свойство | Описание | Значение по умолчанию |
|:--- |:--- |:--- |
| CharacterSet | Персонаж, установленный для использования для сеанса. Например, `CharacterSet=UTF16`.<br><br/>Это значение может быть набором символов, определяемым пользователем, или одним из следующих заранее определенных наборов символов: <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS (Windows, DOS совместимый, KANJISJIS_0S)<br/>- EUC (Unix совместимый, KANJIEC_0U)<br/>- IBM Mainframe (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | Значение по умолчанию: `ASCII`. |
| MaxRespSize |Максимальный размер буфера отклика для запросов S'L в килобайтах (КБ). Например, `MaxRespSize=‭10485760‬`.<br/><br/>Для версии базы данных Teradata 16.00 или позже максимальное значение составляет 7361536. Для соединений, которые используют более ранние версии, максимальное значение составляет 1048576. | Значение по умолчанию: `65536`. |

**Пример с использованием базовой аутентификации**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример использования аутентификации Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Следующая полезная нагрузка по-прежнему поддерживается. Заглядывая вперед, однако, вы должны использовать новый.

**Предыдущие полезные данные:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

В этом разделе приводится список свойств, поддерживаемых набором данных Teradata. Полный список разделов и свойств, доступных для определения наборов данных, [см.](concepts-datasets-linked-services.md)

Для копирования данных из Teradata поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно `TeradataTable`быть установлено. | Да |
| База данных | Название экземпляра Teradata. | Нет (если свойство query указано в источнике действия) |
| table | Название таблицы в экземпляре Teradata. | Нет (если свойство query указано в источнике действия) |

**Примере:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`набор данных типа по-прежнему поддерживается. Однако мы рекомендуем использовать новый набор данных.

**Предыдущие полезные данные:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

В этом разделе содержится список свойств, поддерживаемых Teradata в качестве источника. Полный список разделов и свойств, доступных для определения действий, см. в разделе [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata в качестве источника

>[!TIP]
>Для эффективной загрузки данных из Teradata с помощью раздела данных, узнайте больше из [параллельной копии из раздела Teradata.](#parallel-copy-from-teradata)

Для копирования данных из Teradata в разделе **источника активности** копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования `TeradataSource`должно быть установлено. | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`.<br>При включании разделенной нагрузки необходимо подключить все соответствующие параметры встроенной перегородки в запросе. Например, смотрите [параллельную копию из раздела Teradata.](#parallel-copy-from-teradata) | Нет (если указана таблица в наборе данных) |
| разделыВарианты | Определяет параметры раздела данных, используемые для загрузки данных с Teradata. <br>Допустимые значения: **Нет** (по умолчанию), **Хэш** и **DynamicRange**.<br>При включении параметра раздела (т.е. нет), `None`степень параллелизма для одновременной [`parallelCopies`](copy-activity-performance.md#parallel-copy) загрузки данных из Teradata контролируется параметром на активности копирования. | нет |
| разделНастройки | Укажите группу параметров для раздела данных. <br>Применить, когда параметр раздела не `None`является . | нет |
| разделКоляНайм | Укажите имя исходной столбца, которая будет использоваться разделом диапазона или разделом Хэша для параллельной копии. Если не указано, основной индекс таблицы автоматически обнаруживается и используется в качестве столбца раздела. <br>Применить, когда параметр `Hash` раздела или `DynamicRange`. Если вы используете запрос для получения исходных данных, крючок `?AdfHashPartitionCondition` или `?AdfRangePartitionColumnName` в пункте WHERE. Смотрите пример в [параллельной копии из раздела Teradata.](#parallel-copy-from-teradata) | нет |
| разделUpperBound | Максимальное значение столбца раздела для копирования данных. <br>Применить, когда параметр `DynamicRange`раздела . Если вы используете запрос для получения `?AdfRangePartitionUpbound` исходных данных, подключите положение WHERE. Например, смотрите [параллельную копию из раздела Teradata.](#parallel-copy-from-teradata) | нет |
| разделБолеей предел | Минимальное значение столбца раздела для копирования данных. <br>Применить, когда параметр `DynamicRange`раздела . Если вы используете запрос для извлечения `?AdfRangePartitionLowbound` исходных данных, подключите положение WHERE. Например, смотрите [параллельную копию из раздела Teradata.](#parallel-copy-from-teradata) | нет |

> [!NOTE]
>
> `RelationalSource`источник копии типа по-прежнему поддерживается, но он не поддерживает новую встроенную параллельную нагрузку от Teradata (параметры раздела). Однако мы рекомендуем использовать новый набор данных.

**Пример: копирование данных с помощью базового запроса без раздела**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Параллельная копия из Teradata

Разъем Teradata Data Factory обеспечивает встроенное разъединение данных для параллельной копирования данных из Teradata. Параметры раздела данных можно найти в таблице **«Источник»** действия копирования.

![Скриншот вариантов раздела](./media/connector-teradata/connector-teradata-partition-options.png)

При включании раздела копии Data Factory выполняет параллельные запросы против источника Teradata для загрузки данных разделами. Параллельная степень контролируется настройкой [`parallelCopies`](copy-activity-performance.md#parallel-copy) на активности копирования. Например, если `parallelCopies` вы установите четыре, Data Factory одновременно генерирует и запускает четыре запроса на основе указанного параметра раздела и настроек, и каждый запрос получает часть данных из тераданных.

Предлагается включить параллельную копию с разделом данных, особенно при загрузке большого объема данных с тераданных. Ниже приведены предлагаемые конфигурации для различных сценариев. При копировании данных в хранилище данных на основе файлов рекомендуется записывать в папку в виде нескольких файлов (только указать имя папки), и в этом случае производительность лучше, чем запись в один файл.

| Сценарий                                                     | Рекомендуемые параметры                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Полная нагрузка с большого стола.                                   | **Вариант раздела**: Хэш. <br><br/>Во время выполнения Data Factory автоматически обнаруживает столбец PK, применяет хэш против него и копирует данные по разделам. |
| Загрузите большой объем данных с помощью пользовательского запроса.                 | **Вариант раздела**: Хэш.<br>**Запрос**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Столбец раздела**: Укажите столбец, используемый для нанесения раздела хэша. Если не указано, Data Factory автоматически обнаруживает столбец PK таблицы, указанной в наборе данных Teradata.<br><br>Во время выполнения Фабрика данных заменяется `?AdfHashPartitionCondition` логикой раздела хэша и отправляет в Teradata. |
| Загрузите большой объем данных с помощью пользовательского запроса, имея целый столбец с равномерно распределенным значением для раздела диапазона. | **Параметры раздела**: Динамический раздел диапазона.<br>**Запрос**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Столбец раздела**: Указать столбец, используемый для данных раздела. Можно разгородиться по столбце с типом данных с разреза.<br>**Раздел верхней границы** и **раздел нижней границы**: Укажите, хотите ли вы отфильтровать столбец раздела для получения данных только между нижним и верхним диапазоном.<br><br>Во время выполнения, `?AdfRangePartitionColumnName`Data `?AdfRangePartitionUpbound`Factory `?AdfRangePartitionLowbound` заменяет , и с фактическим именем столбца и диапазонов значений для каждого раздела, и отправляет в Teradata. <br>Например, если столбец раздела "ID" установлен с нижней полосой как 1, а верхний - 80, с параллельным набором копий как 4, Data Factory получает данные четырёми разделами. Их идентионы находятся между 1,20, 21, 40, 41, 60 и 61, 80, соответственно. |

**Пример: запрос с разделом хэша**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Пример: запрос с динамическим перегородкой диапазона**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Сопоставление типов для Teradata

При копировании данных из Teradata применяются следующие отображения. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных Teradata | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| BigInt |Int64 |
| BLOB-объект |Byte[] |
| Byte |Byte[] |
| Тип ByteInt |Int16 |
| CHAR |Строка |
| Clob |Строка |
| Дата |Дата и время |
| Decimal |Decimal |
| Double |Double |
| GRAPHIC |Не поддерживается. Применить явные литые в исходном запросе. |
| Целое число |Int32 |
| Interval Day |Не поддерживается. Применить явные литые в исходном запросе. |
| Interval Day To Hour |Не поддерживается. Применить явные литые в исходном запросе. |
| Interval Day To Minute |Не поддерживается. Применить явные литые в исходном запросе. |
| Interval Day To Second |Не поддерживается. Применить явные литые в исходном запросе. |
| Interval Hour |Не поддерживается. Применить явные литые в исходном запросе. |
| Interval Hour To Minute |Не поддерживается. Применить явные литые в исходном запросе. |
| Interval Hour To Second |Не поддерживается. Применить явные литые в исходном запросе. |
| Interval Minute |Не поддерживается. Применить явные литые в исходном запросе. |
| Interval Minute To Second |Не поддерживается. Применить явные литые в исходном запросе. |
| Interval Month |Не поддерживается. Применить явные литые в исходном запросе. |
| Interval Second |Не поддерживается. Применить явные литые в исходном запросе. |
| Interval Year |Не поддерживается. Применить явные литые в исходном запросе. |
| Interval Year To Month |Не поддерживается. Применить явные литые в исходном запросе. |
| Number |Double |
| Период (Дата) |Не поддерживается. Применить явные литые в исходном запросе. |
| Период (время) |Не поддерживается. Применить явные литые в исходном запросе. |
| Период (Время с временной зоной) |Не поддерживается. Применить явные литые в исходном запросе. |
| Period (Timestamp) |Не поддерживается. Применить явные литые в исходном запросе. |
| Период (Timestamp with Time Zone) |Не поддерживается. Применить явные литые в исходном запросе. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Отметка времени |Дата и время |
| Timestamp With Time Zone |Дата и время |
| VarByte |Byte[] |
| VarChar |Строка |
| VarGraphic |Не поддерживается. Применить явные литые в исходном запросе. |
| Xml |Не поддерживается. Применить явные литые в исходном запросе. |


## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
