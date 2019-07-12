---
title: Копирование данных из таблицы SAP с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из таблицы SAP на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: jingwang
ms.openlocfilehash: 9216f5c00cbdac273b562736abdd1c812d172237
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827756"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Копирование данных из таблицы SAP с помощью фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из таблицы SAP. Дополнительные сведения см. в разделе [об обзоре действия копирования](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно скопировать из таблицы SAP в любой поддерживаемый приемник данных. Список хранилищ данных, которые поддерживаются в качестве источников и приемников, с помощью действия копирования, см. в разделе [поддерживаемых хранилищ данных](copy-activity-overview.md#supported-data-stores-and-formats) таблицы.

В частности этот соединитель таблицы SAP поддерживает:

- Копирование данных из таблицы SAP в:

  - ERP центральный компонент SAP (SAP ECC) версии 7.01 или более поздней версии (в последних SAP поддержки пакета стопка выпущено после 2015).
  - SAP Business Warehouse (SAP BW) 7.01 или более поздней версии.
  - SAP S/4HANA.
  - Другие продукты в SAP Business Suite 7.01 или более поздней версии.

- Копирование данных из прозрачного таблицам SAP, таблицу в составе пула, кластеризованная таблица и представление.
- Копирование данных с помощью обычной проверки подлинности или защитить сеть обмена данными (SNC), если настроен SNC.
- Подключения к серверу приложений SAP или сервер сообщений SAP.

## <a name="prerequisites"></a>предварительные требования

Чтобы использовать этот соединитель SAP таблицы, вам потребуется:

- Настройка локальной среды выполнения интеграции (версия 3.17 или более поздней версии). Дополнительные сведения см. в разделе [создать и настроить локальную среду выполнения интеграции](create-self-hosted-integration-runtime.md).

- Скачать 64-разрядной версии [соединителя SAP для Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) с веб-сайта компании SAP и установите его на компьютере среды выполнения интеграции. Во время установки обязательно выберите **Установка сборок в глобальный кэш СБОРОК** в диалоговом окне **этапы установки** окна.

  ![Установка соединителя SAP для .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Пользователь SAP, который используется в соединителе SAP фабрики данных таблицы необходимо иметь следующие разрешения:

  - Авторизация для использования назначения удаленный вызов функции (RFC).
  - Разрешения на выполнение действия объекта S_SDSAUTH авторизации.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю SAP таблицы.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для SAP BW откройте Центр связанный службы поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| `type` | `type` Свойству должно быть присвоено `SapTable`. | Да |
| `server` | Имя сервера, на котором будет размещен экземпляр SAP.<br/>Используйте для подключения к серверу приложений SAP. | Нет |
| `systemNumber` | Номер системы, системы SAP.<br/>Используйте для подключения к серверу приложений SAP.<br/>Допустимое значение: Двузначное десятичное число, представленное в виде строки. | Нет |
| `messageServer` | Имя узла сервера сообщений SAP.<br/>Используйте для подключения к серверу SAP сообщение. | Нет |
| `messageServerService` | Имя службы или порт номер сервера сообщений.<br/>Используйте для подключения к серверу SAP сообщение. | Нет |
| `systemId` | Идентификатор системы SAP, в которой находится таблица.<br/>Используйте для подключения к серверу SAP сообщение. | Нет |
| `logonGroup` | Группа входа для системы SAP.<br/>Используйте для подключения к серверу SAP сообщение. | Нет |
| `clientId` | Идентификатор клиента в системе SAP.<br/>Допустимое значение: Трехзначное десятичное число, представленное в виде строки. | Да |
| `language` | Язык, используемый системой SAP.<br/>По умолчанию имеет значение `EN`.| Нет |
| `userName` | Имя пользователя, имеющего доступ к серверу SAP. | Да |
| `password` | Пароль пользователя. Пометьте это поле с `SecureString` типа, чтобы безопасно хранить его в фабрике данных, или [ссылка на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| `sncMode` | Индикатор активации SNC для доступа к серверу SAP, в которой находится таблица.<br/>Используйте, если вы хотите использовать SNC для подключения к серверу SAP.<br/>Допустимые значения: `0` (оно отключено, по умолчанию) или `1` (вкл.). | Нет |
| `sncMyName` | Имя инициатора SNC для доступа к серверу SAP, в которой находится таблица.<br/>Применяется, когда `sncMode` включен. | Нет |
| `sncPartnerName` | Обмен данными партнера SNC name для доступа к серверу SAP, в которой находится таблица.<br/>Применяется, когда `sncMode` включен. | Нет |
| `sncLibraryPath` | Где находится таблица библиотека продукта внешней безопасности для доступа к серверу SAP.<br/>Применяется, когда `sncMode` включен. | Нет |
| `sncQop` | Уровень защиты для качества SNC для применения.<br/>Применяется, когда `sncMode` включен. <br/>Допустимые значения: `1` (проверка подлинности), `2` (целостность), `3` (конфиденциальность), `8` (по умолчанию), `9` (максимум). | Нет |
| `connectVia` | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Локальная среда выполнения интеграции является обязательным, как упоминалось ранее в [предварительные требования](#prerequisites). |Да |

**Пример 1. Подключение к серверу приложений SAP**

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

### <a name="example-2-connect-to-an-sap-message-server"></a>Пример 2 Соединиться с сервером сообщений SAP

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

### <a name="example-3-connect-by-using-snc"></a>Пример 3 Подключение с помощью SNC

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

Полный список разделов и свойств для определения наборов данных, см. в разделе [наборы данных](concepts-datasets-linked-services.md). Следующий раздел содержит список свойств, поддерживаемых набором данных таблицы SAP.

Чтобы скопировать данные из и в службу SAP BW откройте Центр связанный, поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| `type` | `type` Свойству должно быть присвоено `SapTableResource`. | Да |
| `tableName` | Имя таблицы для копирования данных из SAP. | Да |

### <a name="example"></a>Пример

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств для определения действий, см. в разделе [конвейеры](concepts-pipelines-activities.md). Следующий раздел содержит список свойств, поддерживаемых источником SAP таблицы.

### <a name="sap-table-as-a-source"></a>Таблица SAP в качестве источника

Чтобы скопировать данные из таблицы SAP, поддерживаются следующие свойства:

| Свойство                         | Описание                                                  | Обязательно для заполнения |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type` Свойству должно быть присвоено `SapTableSource`.         | Да      |
| `rowCount`                         | Число строк, которые требуется извлечь.                              | Нет       |
| `rfcTableFields`                   | Поля (столбцы) для копирования из таблицы SAP. Например, `column0, column1`. | Нет       |
| `rfcTableOptions`                  | Параметры для фильтрации строк из таблицы SAP. Например, `COLUMN0 EQ 'SOMEVALUE'`. См. также оператор таблицы запросов SAP далее в этой статье. | Нет       |
| `customRfcReadTableFunctionModule` | Пользовательский модуль функцию RFC, который может использоваться для чтения данных из таблицы SAP.<br>Для определения того, как данные извлекаются из системы SAP и возвращается к фабрике данных можно использовать пользовательский модуль функции RFC. Пользовательская функция модуль должен иметь интерфейс, реализованный (импорт, экспорт, таблицы), аналогичную `/SAPDS/RFC_READ_TABLE2`, который является интерфейс по умолчанию, используемые фабрикой данных. | Нет       |
| `partitionOption`                  | Механизм секционирования для считывания из таблицы SAP. Поддерживаемые параметры: <ul><li>`None`</li><li>`PartitionOnInt` (обычным целым числом или целочисленных значений с нулевой дополнение, в левой части, например `0000012345`)</li><li>`PartitionOnCalendarYear` (4 цифр в формате «YYYY»)</li><li>`PartitionOnCalendarMonth` (6 цифр в формате «YYYYMM»)</li><li>`PartitionOnCalendarDate` (8 цифр в формате «ГГГГММДД»)</li></ul> | Нет       |
| `partitionColumnName`              | Имя столбца, используемого для секционирования данных.                | Нет       |
| `partitionUpperBound`              | Максимальное значение для столбца, указанного в `partitionColumnName` , будет использоваться для продолжения с секционированием. | Нет       |
| `partitionLowerBound`              | Минимальное значение для столбца, указанного в `partitionColumnName` , будет использоваться для продолжения с секционированием. | Нет       |
| `maxPartitionsNumber`              | Максимальное число секций для разделения данных в.     | Нет       |

>[!TIP]
>Если в таблице SAP есть большое количество данных, таких как несколько миллиардов строк, используйте `partitionOption` и `partitionSetting` для разделения данных на секции меньшего размера. В этом случае данные считываются в каждой секции, и каждой секции данных извлекается с сервера SAP с помощью одного вызова RFC.<br/>
<br/>
>Принимая `partitionOption` как `partitionOnInt` в качестве примера вычисляется количество строк в каждой секции по этой формуле: (общее число строк, в диапазоне `partitionUpperBound` и `partitionLowerBound`) /`maxPartitionsNumber`.<br/>
<br/>
>Чтобы запустить секций в параллельном режиме, чтобы ускорить копирование, настоятельно рекомендуется сделать `maxPartitionsNumber` кратно значение `parallelCopies` свойства. Дополнительные сведения см. в разделе [параллельное копирование](copy-activity-performance.md#parallel-copy).

В `rfcTableOptions`, следующие распространенные операторы запросов SAP можно использовать для фильтрации строк:

| Оператор | Описание |
| :------- | :------- |
| `EQ` | Равно |
| `NE` | Не равно |
| `LT` | Меньше |
| `LE` | Меньше или равно |
| `GT` | Больше |
| `GE` | Больше или равно |
| `LIKE` | Как показано на `LIKE 'Emma%'` |

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
            }
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Сопоставления типов данных для таблицы SAP

При копировании данных из таблицы SAP, следующие сопоставления используются из SAP табличные типы данных для типов промежуточных данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных ABAP в SAP | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| `C` (Строка) | `String` |
| `I` (Integer) | `Int32` |
| `F` (Float) | `Double` |
| `D` (Date) | `String` |
| `T` (Время) | `String` |
| `P` (BCD упакованные, валюты, Decimal, количество) | `Decimal` |
| `N` (Числовой) | `String` |
| `X` (Двоичные данные и Raw) | `String` |

## <a name="next-steps"></a>Следующие шаги

Список хранилищ данных, поддерживаются в качестве источников и приемников с помощью действия копирования в фабрике данных Azure, см. в разделе [поддерживаемых хранилищ данных](copy-activity-overview.md#supported-data-stores-and-formats).
