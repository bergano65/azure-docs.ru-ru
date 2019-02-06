---
title: Копирование данных из SAP Business Warehouse через Open Hub с помощью Фабрики данных Azure | Документация Майкрософт
description: В этой статье описан процесс копирования данных из SAP Business Warehouse (BW) через Open Hub на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере Фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: jingwang
ms.openlocfilehash: 74061eb081fcc7c2c84707f2414a2edfbfde3289
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299543"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Копирование данных из SAP Business Warehouse через Open Hub с помощью Фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в Фабрике данных Azure скопировать данные из SAP Business Warehouse (BW) через Open Hub. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="sap-bw-open-hub-integration"></a>Интеграция SAP BW Open Hub 

[Служба SAP BW Open Hub ](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) — это эффективный способ извлечения данных из SAP BW. На схеме ниже показан один из наиболее распространенных процессов для клиентов в системе SAP. В этом случае данные передаются так: SAP ECC -> PSA -> DSO -> куб.

Целевое расположение SAP BW Open Hub (OHD) позволяет определить целевой объект, в который передаются данные SAP. Объекты, поддерживаемые процессом передачи данных (DTP) SAP, можно использовать в качестве источника данных Open Hub (например, DSO, InfoCube, MultiProvider, DataSource и др.). В качестве типа целевого расположения в Open Hub для хранения передаваемых данных можно использовать таблицы базы данных (хранящиеся в локальном или удаленном ресурсе) и неструктурированные файлы. Этот соединитель SAP BW Open Hub поддерживает копирование данных из локальной таблицы OHD в BW. Если вы используете другие типы данных, с можно напрямую подключаться к базе данных или файловой системе помощью других соединителей.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из SAP Business Warehouse можно скопировать через Open Hub в любое поддерживаемое хранилище данных, выступающее в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель SAP Business Warehouse Open Hub поддерживает:

- SAP Business Warehouse **7.30 или более поздней версии (в последнем стеке пакета поддержки SAP, выпущенном после 2015 года)**;
- копирование данных через локальную целевую таблицу Open Hub (DSO, InfoCube, MultiProvider, DataSource и др.);
- Копирование данных с помощью базовой проверки подлинности.
- подключение к серверу приложений.

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать этот соединитель SAP Business Warehouse Open Hub, сделайте следующее:

- Настройте локальную среду выполнения интеграции (IR) 3.13 или более поздней версии. Дополнительные сведения см. в статье [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md).

- Скачайте **64-разрядную версию [соединителя SAP .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html)** с сайта SAP и установите ее на компьютере с локальной средой IR. При установке в окне настройки дополнительных параметров обязательно выберите параметр **Install Assemblies to GAC** (Установить сборки в глобальный кэш сборок), как показано на следующем рисунке. 

    ![Установка соединителя SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Пользователь SAP, который используется в соединителе BW Фабрики данных, должен иметь следующие разрешения: 

    - Авторизация в RFC и SAP BW. 
    - Разрешения на действие "Выполнение" для объекта авторизации S_SDSAUTH.

- Создайте тип целевого расположения SAP Open Hub **Таблица базы данных** с включенным параметром Technical Key (Технический ключ).  Рекомендуется также снять флажок Deleting Data from Table (Удаление данных из таблицы), хоть это и не обязательно. Используйте процесс передачи данных (выполните его напрямую или интегрируйте в цепочку существующего процесса) для переноса данных из выбранного исходного объекта (например, куба OLAP) в целевую таблицу Open Hub.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к соединителю SAP Business Warehouse Open Hub.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SAP Business Warehouse (BW) Open Hub поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойству type необходимо задать значение **SapOpenHub** | Yes |
| server | Имя сервера, на котором размещен экземпляр SAP Business Warehouse. | Yes |
| systemNumber | Номер системы SAP Business Warehouse.<br/>Допустимые значения: двузначное десятичное число, представленное в виде строки. | Yes |
| clientid | Идентификатор клиента в системе SAP Business Warehouse.<br/>Допустимые значения: трехзначное десятичное число, представленное в виде строки. | Yes |
| Язык | Язык, используемый в системе SAP. | Нет (по умолчанию задано значение **EN**)|
| userName | Имя пользователя, имеющего доступ к серверу SAP. | Yes |
| password | Пароль для пользователя Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Требуется локальная среда IR, как упоминалось в разделе [Предварительные требования](#prerequisites). |Yes |

**Пример.**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел содержит список свойств, поддерживаемых набором данных Salesforce.

Чтобы скопировать данные из или в SAP BW Open Hub, для свойства type набора данных установите значение **SapOpenHubTable**. Поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Задайте для свойства type значение **SapOpenHubTable**.  | Yes |
| openHubDestinationName | Имя целевого расположения Open Hub, из которого копируются данные. | Yes |
| excludeLastRequest | Нужно ли исключать записи из результатов последнего запроса. | Нет (по умолчанию задано значение **true**) |
| baseRequestId | Идентификатор запроса для загрузки разностных данных. После установки этого параметра в результатах запроса будут отображаться только данные с requestId, значение которого **больше**, чем значение этого свойства.  | Нет  |

>[!TIP]
>Если в вашей таблице Open Hub содержатся только данные, полученные по одному идентификатору запроса, например, вы всегда выполняете полную загрузку и перезаписываете существующие данные в таблице или запускаете DTP только один раз для тестирования, чтобы скопировать данные, не забудьте снять флажок excludeLastRequest.

**Пример.**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником данных SAP BW Open Hub.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub в качестве источника данных

Чтобы скопировать данные из SAP BW Open Hub, установите в действии копирования тип источника **RelationalSource**. На данный момент дополнительные свойства, зависящие от типа и используемые в разделе настройки действий копирования **источника**, отсутствуют.

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Сопоставление типов данных для SAP BW Open Hub

При копировании данных из SAP BW Open Hub используются следующие сопоставления типов данных SAP BW с промежуточными типами данных Фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных ABAP в SAP | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| C (строка) | Строка |
| I (целое число) | Int32 |
| F (с плавающей запятой) | Double |
| D (дата) | Строка |
| T (время) | Строка |
| P (пакеты данных конфигурации загрузки, валюта, десятичное число, количество) | Decimal |
| N (числовой символ) | Строка |
| X (двоичные и необработанные данные) | Строка |

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
