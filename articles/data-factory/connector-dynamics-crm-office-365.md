---
title: Копирование данных из Dynamics CRM или Dynamics 365 (Common Data Service) и в эти решения с помощью фабрики данных Azure | Документация Майкрософт
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
ms.date: 07/01/2019
ms.author: jingwang
ms.openlocfilehash: 18fdb14430eee97ff2780d963abf3e5ceafe1126
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009396"
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
| Dynamics 365 Online <br> Dynamics CRM Online | Office365 | [Проверка подлинности Dynamics Online и Office 365](#dynamics-365-and-dynamics-crm-online) |
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
>[Соединитель Dynamics AX](connector-dynamics-ax.md) также можно использовать для копирования данных из **Dynamics 365 Finance and Operations**.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для Dynamics.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Dynamics поддерживаются следующие свойства:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 и Dynamics CRM Online

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Dynamics**. | Да |
| deploymentType | Тип развертывания для экземпляра Dynamics. Должен иметь значение **Online** для Dynamics Online. | Да |
| serviceUri | URL-адрес вашего экземпляра службы Dynamics, например `https://adfdynamics.crm.dynamics.com`. | Да |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Укажите **Office365** для Dynamics Online. | Да |
| username | Укажите имя пользователя для подключения к Dynamics. | Да |
| password | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
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

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Dynamics**. | Да |
| deploymentType | Тип развертывания для экземпляра Dynamics. Для Dynamics (локальная версия) с IFD необходимо задать значение **OnPremisesWithIfd**.| Да |
| hostName | Имя узла локального сервера Dynamics. | Да |
| port | Порт локального сервера Dynamics. | Нет, значение по умолчанию — 443 |
| organizationName | Имя организации экземпляра Dynamics. | Да |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Укажите **Ifd** для Dynamics (локальная версия) с IFD. | Да |
| username | Укажите имя пользователя для подключения к Dynamics. | Да |
| password | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и передавать его оттуда в действие копирования при фактическом копировании данных. Подробнее это описано в статье [о хранении учетных данных в Key Vault](store-credentials-in-key-vault.md). | Да |
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

Чтобы копировать данные из Dynamics и обратно, задайте для свойства type набора данных значение **DynamicsEntity**. Поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **DynamicsEntity**. |Да |
| entityName | Логическое имя сущности, которое требуется получить. | "Нет" для источника (если свойство query указано в источнике действия), "Да" для приемника |

> [!IMPORTANT]
>- При копировании данных из Dynamics раздел Structure является необязательным, но при этом в наборе данных Dynamics переносится с помощью команды, чтобы обеспечить детерминированный результат копирования. Он определяет столбец имени и тип данных для данных Dynamics, которые требуется скопировать. Дополнительные сведения см. в разделах [Структура набора данных](concepts-datasets-linked-services.md#dataset-structure-or-schema) и [Сопоставление типов данных для Dynamics](#data-type-mapping-for-dynamics).
>- При импорте схемы в пользовательском интерфейсе разработки ADF выводит схему путем выборки верхних строк из результата запроса Dynamics для инициализации создания структуры. При этом столбцы без значений будут опущены. Такое же поведение применяется к выполнению копирования, если нет явного определения структуры. Вы можете просмотреть схему и при необходимости добавить в нее или структуру набора данных Dynamics дополнительные столбцы, которые будут учитываться во время копирования.
>- При копировании данных в Dynamics раздел structure является необязательным в наборе данных Dynamics. Столбцы для копирования определяются схемой источника данных. Если источником является CSV-файл без заголовка, во входном наборе данных укажите параметр structure с именем столбца и тип данных. Они последовательно сопоставляются с полями в CSV-файле.

**Пример.**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
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

Чтобы копировать данные из Dynamics, установите тип источника **DynamicsSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **DynamicsSource**. | Да |
| query | FetchXML — это защищаемый язык запросов, используемый в Dynamics (Online и локальная версия). См. указанный ниже пример. Дополнительные сведения см. в статье [Создание запросов с помощью FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | "Нет" (если для набора данных задано свойство entityName) |

>[!NOTE]
>Столбец первичного ключа будет копироваться всегда, даже если он отсутствует в проекции столбца, настроенной в запросе FetchXML.

**Пример.**

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

Чтобы копировать данные в Dynamics, установите тип приемника **DynamicsSink** в действии копирования. В разделе **sink** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type приемника действия копирования должно иметь значение **DynamicsSink**. | Да |
| writeBehavior | Поведение операции при записи.<br/>Допустимое значение: **Upsert**. | Да |
| writeBatchSize | Количество строк данных, записываемых в Dynamics в каждом пакете. | Нет (значение по умолчанию — 10) |
| ignoreNullValues | Указывает, следует ли игнорировать значения NULL из входных данных (за исключением ключевых полей) во время операции записи.<br/>Допустимые значения: **true** и **false**.<br>- **True**: при выполнении операции upsert или update оставьте данные в целевом объекте неизменными. При выполнении операции вставки (insert) вставьте определенное значение по умолчанию.<br/>- **False**: при выполнении операции upsert или update обновите данные в целевом объекте до значения NULL. При выполнении операции вставки (insert) вставьте значение NULL. | Нет (по умолчанию используется значение false) |

>[!NOTE]
>Значение по умолчанию для приемника "**writeBatchSize**" и операция копирования " **[parallelCopies](copy-activity-performance.md#parallel-copy)** " для приемника Dynamics равны 10. Таким образом в Dynamics одновременно отправляются 100 записей.

Для Dynamics 365 в сети существует ограничение на [2 одновременных пакетных вызова на организацию](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Если этот предел превышен, возникает ошибка "сервер занят" до того, как будет выполнен первый запрос. Поддержание "writeBatchSize", меньшее или равное 10, позволит избежать такого регулирования количества одновременных вызовов.

Оптимальное сочетание "**writeBatchSize**" и "**parallelCopies**" зависит от схемы вашего объекта, например количество столбцов, размер строки, количество подключаемых модулей/рабочих процессов/рабочих процессов, подключенных к этим вызовам и т. д. Значение по умолчанию 10 writeBatchSize * 10 parallelCopies — это рекомендация в соответствии с сервисом Dynamics, которая будет работать для большинства объектов Dynamics, но может иметь не самую лучшую производительность. Вы можете настроить производительность путем корректировки комбинации в настройках активности копирования.

**Пример.**

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
| AttributeTypeCode.BigInt | Long | ✓ | ✓ |
| AttributeTypeCode.Boolean | логический | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Строковое | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (связанный с одним объектом) |
| AttributeType.ManagedProperty | логический | ✓ | |
| AttributeType.Memo | Строковое | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | Строковое | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Типы данных Dynamics AttributeType. CalendarRules, AttributeType. Мултиселектпикклист и AttributeType. PartyList не поддерживаются.

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Следующие шаги
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
