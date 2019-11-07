---
title: Копирование данных из Dynamics CRM или Dynamics 365 (Common Data Service) и обратно с помощью фабрики данных Azure
description: Узнайте, как копировать данные из Microsoft Dynamics CRM или Microsoft Dynamics 365 (Common Data Service) в поддерживаемые хранилища данных, используемые в качестве приемника, или из поддерживаемых исходных хранилищ данных в Dynamics CRM и Dynamics 365 с помощью действия копирования в конвейере фабрики данных.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: c9adcf72eeec82fd4b8f1805fca1f284c0b953b7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680982"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Копирование данных из Dynamics 365 (Common Data Service) или Dynamics CRM и в эти решения с помощью фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Microsoft Dynamics 365 или Microsoft Dynamics CRM и обратно. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из Dynamics 365 (Common Data Service) или Dynamics CRM можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. В свою очередь, данные из любого хранилища данных, поддерживаемого в качестве источника, можно скопировать в Dynamics 365 (Common Data Service) или Dynamics CRM. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель Dynamics поддерживает версию Dynamics 7. x до 9. x как в сети, так и в локальной среде. В частности:

- Версия 7. x сопоставляется с Dynamics CRM 2015
- Версия 8. x сопоставляется с Dynamics CRM 2016 и ранней версией Dynamics 365
- Версия 9. x сопоставляется с более поздней версией Dynamics 365

В следующей таблице приведены поддерживаемые типы проверки подлинности и конфигурации для соответствующих версий и продуктов Dynamics. (IFD — сокращение от термина "развертывание с выходом в Интернет".)

| Версии Dynamics | Типы проверки подлинности | Примеры связанной службы |
|:--- |:--- |:--- |
| Dynamics 365 Online <br> Dynamics CRM Online | Оffice 365 | [Проверка подлинности Dynamics Online и Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 (локальная версия) с IFD <br> Dynamics CRM 2016 (локальная версия) с IFD <br> Dynamics CRM 2015 (локальная версия) с IFD | IFD | [Dynamics (локальная версия) с IFD и проверка подлинности IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

В частности, для Dynamics 365 поддерживаются следующие типы приложений:

- Dynamics 365 for Sales;
- Dynamics 365 for Customer Service;
- Dynamics 365 for Field Service;
- Dynamics 365 for Project Service Automation;
- Dynamics 365 for Marketing.

Другие типы приложений (например, Dynamics 365 for Finance and Operations, Dynamics 365 for Talent и т. д.) не поддерживаются в этом соединителе.

Этот соединитель Dynamics построен на основе [инструментов Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>**Соединитель Dynamics AX** также можно использовать для копирования данных из [Dynamics 365 Finance and Operations](connector-dynamics-ax.md).

## <a name="get-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для Dynamics.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Dynamics поддерживаются следующие свойства:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 и Dynamics CRM Online

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства Type необходимо задать значение **Dynamics**, **динамикскрм**или **коммондатасервицефораппс**. | Да |
| deploymentType | Тип развертывания для экземпляра Dynamics. Должен иметь значение **Online** для Dynamics Online. | Да |
| serviceUri | URL-адрес вашего экземпляра службы Dynamics, например `https://adfdynamics.crm.dynamics.com`. | Да |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Укажите **Office365** для Dynamics Online. | Да |
| Имя пользователя | Укажите имя пользователя для подключения к Dynamics. | Да |
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника, если связанная с источником служба не имеет среды выполнения интеграции |

>[!NOTE]
>Соединитель Dynamics, используемый для необязательного свойства organizationName, чтобы идентифицировать экземпляр Dynamics CRM или 365 Online. Во время работы соединителя вам предлагается вместо этого указать новое свойство serviceUri, чтобы повысить производительность обнаружения экземпляра.

**Пример. Dynamics Online с использованием проверки подлинности Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 и Dynamics CRM (локальная версия) с IFD

*hostName и port являются дополнительными свойствами по сравнению с Dynamics Online.*

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства Type необходимо задать значение **Dynamics**, **динамикскрм**или **коммондатасервицефораппс**. | Да |
| deploymentType | Тип развертывания для экземпляра Dynamics. Для Dynamics (локальная версия) с IFD необходимо задать значение **OnPremisesWithIfd**.| Да |
| hostName | Имя узла локального сервера Dynamics. | Да |
| порт | Порт локального сервера Dynamics. | Нет, значение по умолчанию — 443 |
| оrganizationName | Имя организации экземпляра Dynamics. | Да |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Укажите **Ifd** для Dynamics (локальная версия) с IFD. | Да |
| Имя пользователя | Укажите имя пользователя для подключения к Dynamics. | Да |
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и передавать его оттуда в действие копирования при фактическом копировании данных. Подробнее это описано в статье [о хранении учетных данных в Key Vault](store-credentials-in-key-vault.md). | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника |

**Пример. Dynamics (локальная версия) с IFD с использованием проверки подлинности**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Dynamics.

Для копирования данных из и в Dynamics поддерживаются следующие свойства.

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство Type набора данных должно иметь значение **DynamicsEntity**, **динамикскрментити**или **коммондатасервицефораппсентити**. |Да |
| entityName | Логическое имя сущности, которое требуется получить. | "Нет" для источника (если свойство query указано в источнике действия), "Да" для приемника |

**Пример**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых типами источника и приемника Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics в качестве источника данных

Чтобы скопировать данные из Dynamics, в разделе **источник** действия копирования поддерживаются следующие свойства.

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство Type источника действия копирования должно иметь значение **DynamicsSource**, **динамикскрмсаурце**или **коммондатасервицефораппссаурце**. | Да |
| запрос | FetchXML — это защищаемый язык запросов, используемый в Dynamics (Online и локальная версия). См. указанный ниже пример. Дополнительные сведения см. в статье [Создание запросов с помощью FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | "Нет" (если для набора данных задано свойство entityName) |

>[!NOTE]
>Столбец первичного ключа будет копироваться всегда, даже если он отсутствует в проекции столбца, настроенной в запросе FetchXML.

> [!IMPORTANT]
>- При копировании данных из Dynamics явное сопоставление столбцов из Dynamics с приемником является необязательным, но с высокой повторной назначением для обеспечения детерминированного результата копирования.
>- При импорте схемы в пользовательском интерфейсе ADF определяет схему, выполнив выборку первых строк из результата запроса Dynamics для инициализации списка исходных столбцов. в этом случае столбцы без значений в верхних строках будут опущены. Такое же поведение применяется к выполнению копирования, если нет явного сопоставления. В сопоставление можно проверить и добавить дополнительные столбцы, которые будут учитываться во время выполнения копирования.

**Пример**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Образец запроса FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics в качестве типа приемника

Чтобы скопировать данные в Dynamics, в разделе **приемника** действия копирования поддерживаются следующие свойства.

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство Type приемника действия копирования должно иметь значение **DynamicsSink**, **динамикскрмсинк**или **коммондатасервицефораппссинк**. | Да |
| writeBehavior | Поведение операции при записи.<br/>Допустимое значение: **Upsert**. | Да |
| алтернатекэйнаме | Укажите альтернативное имя ключа, определенное в сущности, для выполнения "Upsert". | Нет |
| writeBatchSize | Количество строк данных, записываемых в Dynamics в каждом пакете. | Нет (значение по умолчанию — 10) |
| ignoreNullValues | Указывает, следует ли игнорировать значения NULL из входных данных (за исключением ключевых полей) во время операции записи.<br/>Допустимые значения: **true** и **false**.<br>- **True**: при выполнении операции upsert или update оставьте данные в целевом объекте неизменными. При выполнении операции вставки (insert) вставьте определенное значение по умолчанию.<br/>- **False**: при выполнении операции upsert или update обновите данные в целевом объекте до значения NULL. При выполнении операции вставки (insert) вставьте значение NULL. | Нет (по умолчанию используется значение false) |

>[!NOTE]
>Значение по умолчанию для приемника "**writeBatchSize**" и операция копирования " **[parallelCopies](copy-activity-performance.md#parallel-copy)** " для приемника Dynamics равны 10. Таким образом в Dynamics одновременно отправляются 100 записей.

Для Dynamics 365 в сети существует ограничение на [2 одновременных пакетных вызова на организацию](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Если этот предел превышен, возникает ошибка "сервер занят" до того, как будет выполнен первый запрос. Поддержание "writeBatchSize", меньшее или равное 10, позволит избежать такого регулирования количества одновременных вызовов.

Оптимальное сочетание "**writeBatchSize**" и "**parallelCopies**" зависит от схемы сущности, например число столбцов, размер строк, число подключаемых модулей, рабочих процессов и действий рабочих процессов, подключенных к этим вызовам, и т. д. Значение по умолчанию 10 writeBatchSize * 10 parallelCopies является рекомендацией в соответствии со службой Dynamics, которая будет работать для большинства сущностей Dynamics, но может не быть лучшей производительностью. Вы можете настроить производительность путем корректировки комбинации в настройках активности копирования.

**Пример**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Сопоставление типов данных для Dynamics

При копировании данных из Dynamics для промежуточных типов данных фабрики данных используются следующие сопоставления из типов данных Dynamics. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

Настройте соответствующие типы данных для фабрики данных в структуре набора данных на основе вашего типа данных источника Dynamics, используя следующую таблицу сопоставления.

| Тип данных Dynamics | Тип промежуточных данных фабрики данных | Поддерживается в качестве источника | Поддерживается в качестве приемника |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | длинное целое | ✓ | ✓ |
| AttributeTypeCode.Boolean | Логический | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | DECIMAL | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | string | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (связанный с одним объектом) |
| AttributeType.ManagedProperty | Логический | ✓ | |
| AttributeType.Memo | string | ✓ | ✓ |
| AttributeType.Money | DECIMAL | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | string | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Типы данных Dynamics AttributeType. CalendarRules, AttributeType. Мултиселектпикклист и AttributeType. PartyList не поддерживаются.

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
