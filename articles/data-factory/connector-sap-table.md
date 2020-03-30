---
title: Копирование данных из таблицы SAP
description: Узнайте, как скопировать данные из таблицы SAP в поддерживаемые хранилища данных раковины, используя действие копирования в конвейере Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: e98ff7fd914bb86cae256bb1bf6c19086758d463
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371546"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Копирование данных из таблицы SAP с помощью Azure Data Factory

В этой статье излагается, как использовать активность копирования в Azure Data Factory для копирования данных из таблицы SAP. Для получения дополнительной информации смотрите [обзор активности Copy](copy-activity-overview.md).

>[!TIP]
>Чтобы узнать общую поддержку ADF по сценарию интеграции данных SAP, см. [интеграцию данных SAP с помощью whitepaper Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) с подробным введением, компарсом и руководством.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем таблицы SAP поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Вы можете скопировать данные из таблицы SAP в любой поддерживаемый хранилище данных раковины. Для списка хранилиров данных, которые поддерживаются в качестве источников или появляются в потоке активности копирования, см. [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats)

В частности, этот разъем таблицы SAP поддерживает:

- Копирование данных из таблицы SAP в:

  - SAP ERP Central Component (SAP ECC) версия 7.01 или более поздняя (в недавнем пакете пакетов поддержки SAP, выпущенном после 2015 года).
  - SAP Business Warehouse (SAP BW) версия 7.01 или более поздняя (в недавнем пакете пакетов поддержки SAP, выпущенном после 2015 года).
  - SAP S/4HANA.
  - Другие продукты в версии SAP Business Suite 7.01 или более поздней версии (в недавнем пакете пакетов поддержки SAP, выпущенном после 2015 года).

- Копирование данных как из прозрачной таблицы SAP, так и с объединенной таблицы, кластерной таблицы и представления.
- Копирование данных с помощью базовой аутентификации или безопасной сетевой связи (SNC), если SNC настроен.
- Подключение к серверу приложений SAP или серверу сообщений SAP.

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать этот разъем таблицы SAP, необходимо:

- Настройка автономного времени выполнения интеграции (версия 3.17 или позже). Для получения дополнительной информации [см. Создать и настроить автономное время выполнения интеграции.](create-self-hosted-integration-runtime.md)

- Загрузите 64-разрядный [sAP-разъем для Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) с веб-сайта SAP и установите его на самохлаженную интеграционную машину. Во время установки убедитесь, что вы выбираете **сборки установки для** опции GAC в окне **факультативной настройки.**

  ![Установка SAP-разъема для .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Пользователь SAP, используемый в разъеме таблицы Data Factory SAP, должен иметь следующие разрешения:

  - Авторизация для использования направлений удаленного вызова функций (RFC).
  - Разрешения на выполнение действия объекта авторизации S_SDSAUTH.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах приводится подробная информация о свойствах, используемых для определения сущностей фабрики данных, специфичных для разъема таблицы SAP.

## <a name="linked-service-properties"></a>Свойства связанной службы

Следующие свойства поддерживаются для службы SAP BW Open Hub, связанной с:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| `type` | Для свойства `type` должно быть установлено значение `SapTable`. | Да |
| `server` | Название сервера, на котором расположен экземпляр SAP.<br/>Используйте для подключения к серверу приложений SAP. | нет |
| `systemNumber` | Номер системы системы SAP.<br/>Используйте для подключения к серверу приложений SAP.<br/>Разрешенное значение: двузначное десятичное число, представленное в виде строки. | нет |
| `messageServer` | Имя хоста сервера сообщений SAP.<br/>Используйте для подключения к серверу сообщений SAP. | нет |
| `messageServerService` | Имя службы или номер порта сервера сообщений.<br/>Используйте для подключения к серверу сообщений SAP. | нет |
| `systemId` | Идентификатор системы SAP, в которой находится таблица.<br/>Используйте для подключения к серверу сообщений SAP. | нет |
| `logonGroup` | Группа логово для системы SAP.<br/>Используйте для подключения к серверу сообщений SAP. | нет |
| `clientId` | Идентификатор клиента в системе SAP.<br/>Разрешенное значение: трехзначное десятичное число, представленное в виде строки. | Да |
| `language` | Язык, который использует система SAP.<br/>Значение по умолчанию: `EN`.| нет |
| `userName` | Имя пользователя, который имеет доступ к серверу SAP. | Да |
| `password` | Пароль пользователя. Отметьте это `SecureString` поле с типом для безопасного хранения на фабрике данных или [отосведите секрет, хранящийся в Хранилище ключей Azure.](store-credentials-in-key-vault.md) | Да |
| `sncMode` | Индикатор активации SNC для доступа к серверу SAP, на котором находится таблица.<br/>Используйте, если вы хотите использовать SNC для подключения к серверу SAP.<br/>Разрешенные значения `0` (выкл, `1` по умолчанию) или (на). | нет |
| `sncMyName` | Имя SNC инициатора для доступа к серверу SAP, где находится таблица.<br/>Применяется, `sncMode` когда он находится на. | нет |
| `sncPartnerName` | SNC имя партнера связи для доступа к серверу SAP, где находится таблица.<br/>Применяется, `sncMode` когда он находится на. | нет |
| `sncLibraryPath` | Библиотека внешнего продукта безопасности для доступа к серверу SAP, на котором находится таблица.<br/>Применяется, `sncMode` когда он находится на. | нет |
| `sncQop` | Уровень качества защиты SNC для применения.<br/>Применяется, `sncMode` когда он находится на. <br/>Разрешенные `1` `2` значения (Аутентификация), (Целостность), `3` (Конфиденциальность), `8` (по умолчанию), `9` (Максимум). | нет |
| `connectVia` | [Время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Требуется самоходное время выполнения интеграции, как упоминалось ранее в [Prerequisites.](#prerequisites) |Да |

**Пример 1: Подключение к серверу приложений SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Пример 2: Подключение к серверу сообщений SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Пример 3: Подключение с помощью SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств для определения наборов данных можно узнать на примере [наборов данных.](concepts-datasets-linked-services.md) В следующем разделе приводится список свойств, поддерживаемых набором данных таблицы SAP.

Для копирования данных из и в службе SAP BW Open Hub, связанной с SAP BW, поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| `type` | Для свойства `type` должно быть установлено значение `SapTableResource`. | Да |
| `tableName` | Название таблицы SAP для копирования данных. | Да |

### <a name="example"></a>Пример

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств для определения деятельности можно узнать на примере [pipelines](concepts-pipelines-activities.md). В следующем разделе приводится список свойств, поддерживаемых источником таблицы SAP.

### <a name="sap-table-as-source"></a>Таблица SAP как источник

Для копирования данных из таблицы SAP поддерживаются следующие свойства:

| Свойство                         | Описание                                                  | Обязательно |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | Для свойства `type` должно быть установлено значение `SapTableSource`.         | Да      |
| `rowCount`                         | Количество строк, которые необходимо получить.                              | нет       |
| `rfcTableFields`                   | Поля (столбцы) для копирования из таблицы SAP. Например, `column0, column1`. | нет       |
| `rfcTableOptions`                  | Параметры фильтрации строк в таблице SAP. Например, `COLUMN0 EQ 'SOMEVALUE'`. Позже в этой статье смотрите таблицу оператора запросов SAP. | нет       |
| `customRfcReadTableFunctionModule` | Пользовательский модуль функции RFC, который можно использовать для чтения данных из таблицы SAP.<br>Можно использовать пользовательский модуль функции RFC для определения того, как данные извлекаются из вашей системы SAP и возвращаются на фабрику данных. Модуль пользовательской функции должен иметь реализованный интерфейс (импорт, `/SAPDS/RFC_READ_TABLE2`экспорт, таблицы), который похож на интерфейс по умолчанию, используемый Data Factory. | нет       |
| `partitionOption`                  | Механизм раздела для чтения из таблицы SAP. Поддерживаемые варианты перечислены ниже. <ul><li>`None`</li><li>`PartitionOnInt`(нормальные целые или целые значения с нулевым обивка слева, такие как `0000012345`)</li><li>`PartitionOnCalendarYear`(4 цифры в формате "YYYY")</li><li>`PartitionOnCalendarMonth`(6 цифр в формате «YYYMM»)</li><li>`PartitionOnCalendarDate`(8 цифр в формате «YYYMMDD»)</li></ul> | нет       |
| `partitionColumnName`              | Название столбца, используемое для раздела данных.                | нет       |
| `partitionUpperBound`              | Максимальное значение столбца, `partitionColumnName` указанное в этом, будет использоваться для продолжения раздела. | нет       |
| `partitionLowerBound`              | Минимальное значение столбца, `partitionColumnName` указанное в этом, будет использоваться для продолжения раздела. (Примечание: `partitionLowerBound` не может быть "0" `PartitionOnInt`при варианте раздела) | нет       |
| `maxPartitionsNumber`              | Максимальное количество разделов для разделения данных на.     | нет       |

>[!TIP]
>Если в таблице SAP имеется большой объем данных, `partitionOption` `partitionSetting` например несколько миллиардов строк, используйте и разделите данные на более мелкие разделы. В этом случае данные считываются на раздел, и каждый раздел данных извлекается с вашего сервера SAP с помощью одного вызова RFC.<br/>
<br/>
>Если `partitionOption` `partitionOnInt` взять в качестве примера, то количество строк в каждой раздельном составе рассчитывается с помощью этой формулы: (общие строки, падающие между `partitionUpperBound` и)/ `partitionLowerBound``maxPartitionsNumber`.<br/>
<br/>
>Для загрузки разделов данных параллельно с ускорением копирования [`parallelCopies`](copy-activity-performance.md#parallel-copy) параллельная степень контролируется установкой на действия копирования. Например, если `parallelCopies` вы установите четыре, Data Factory одновременно генерирует и запускает четыре запроса на основе указанного параметра раздела и настроек, и каждый запрос извлекает часть данных из таблицы SAP. Мы настоятельно `maxPartitionsNumber` рекомендуем сделать кратное значение `parallelCopies` имущества. При копировании данных в хранилище данных, основанных на файлах, он также рекомендуется записывать в папку как несколько файлов (только указать имя папки), и в этом случае производительность лучше, чем запись в один файл.

Для `rfcTableOptions`фильтрации строк можно использовать следующие общие операторы запросов SAP:

| Оператор | Описание |
| :------- | :------- |
| `EQ` | Равно |
| `NE` | Не равно |
| `LT` | Меньше чем |
| `LE` | Меньше или равно |
| `GT` | Больше |
| `GE` | Больше или равно |
| `LIKE` | Как в`LIKE 'Emma%'` |

### <a name="example"></a>Пример

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Отображение типов данных для таблицы SAP

При копировании данных из таблицы SAP используются следующие отображения от типов данных таблицы SAP до промежуточных типов данных Azure Data Factory. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных ABAP в SAP | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| `C`(Струна) Я не стою. | `String` |
| `I`(Все в оне) | `Int32` |
| `F`(Поплавок) Я не стою | `Double` |
| `D`(Дата) | `String` |
| `T`(Время) Время. | `String` |
| `P`(BCD Упакованные, Валюта, Десятичная, Йти) | `Decimal` |
| `N`(Число) | `String` |
| `X`(Двоичный и сырье) | `String` |

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Дальнейшие действия

Для списка хранилищ данных, поддерживаемых в качестве источников и поглотителей в результате активности копирования на фабрике данных Azure, [см.](copy-activity-overview.md#supported-data-stores-and-formats)
