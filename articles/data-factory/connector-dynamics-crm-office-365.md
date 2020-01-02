---
title: Копирование данных в Dynamics (Common Data Service)
description: Узнайте, как копировать данные из Microsoft Dynamics CRM или Microsoft Dynamics 365 (Common Data Service) в поддерживаемые хранилища данных, используемые в качестве приемника, или из поддерживаемых исходных хранилищ данных в Dynamics CRM и Dynamics 365 с помощью действия копирования в конвейере фабрики данных.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: d065439839ba5db479305ae81c61892cb5cf5e70
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929452"
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
| Common Data Service <br> Dynamics 365 Online <br> Dynamics CRM Online | Субъект-служба AAD <br> Оffice 365 | [Проверка подлинности в Dynamics Online + AAD субъекта-службы или Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 (локальная версия) с IFD <br> Dynamics CRM 2016 (локальная версия) с IFD <br> Dynamics CRM 2015 (локальная версия) с IFD | IFD | [Dynamics (локальная версия) с IFD и проверка подлинности IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

В частности, для Dynamics 365 поддерживаются следующие типы приложений:

- Dynamics 365 for Sales;
- Dynamics 365 for Customer Service;
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation;
- Dynamics 365 for Marketing.

Другие типы приложений (например, Dynamics 365 for Finance and Operations, Dynamics 365 for Talent и т. д.) не поддерживаются в этом соединителе.

Этот соединитель Dynamics построен на основе [инструментов Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>[Соединитель Dynamics AX](connector-dynamics-ax.md) также можно использовать для копирования данных из **Dynamics 365 Finance and Operations**.

## <a name="get-started"></a>Начать

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для Dynamics.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Dynamics поддерживаются следующие свойства:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 и Dynamics CRM Online

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Для свойства Type необходимо задать значение **Dynamics**, **динамикскрм**или **коммондатасервицефораппс**. | ДА |
| deploymentType | Тип развертывания для экземпляра Dynamics. Должен иметь значение **Online** для Dynamics Online. | ДА |
| serviceUri | URL-адрес вашего экземпляра службы Dynamics, например `https://adfdynamics.crm.dynamics.com`. | ДА |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Допустимые значения: **аадсервицепринЦипал** или **"Office 365"** . | ДА |
| servicePrincipalId | Укажите идентификатор клиента приложения Azure Active Directory. | Да при использовании проверки подлинности `AADServicePrincipal` |
| сервицепринЦипалкредентиалтипе | Укажите тип учетных данных для использования при аутентификации субъекта-службы. Допустимые значения: **сервицепринЦипалкэй** или **сервицепринЦипалцерт**. | Да при использовании проверки подлинности `AADServicePrincipal` |
| сервицепринЦипалкредентиал | Укажите учетные данные субъекта-службы. <br>При использовании `ServicePrincipalKey` в качестве типа учетных данных `servicePrincipalCredential` может быть строкой (ADF будет шифровать его при развертывании связанной службы) или ссылаться на секрет в AKV. <br>При использовании `ServicePrincipalCert` в качестве учетных данных `servicePrincipalCredential` должны быть ссылкой на сертификат в AKV. | Да при использовании проверки подлинности `AADServicePrincipal` | 
| Имя пользователя | Укажите имя пользователя для подключения к Dynamics. | Да при использовании проверки подлинности `Office365` |
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да при использовании проверки подлинности `Office365` |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника, если связанная с источником служба не имеет среды выполнения интеграции |

>[!NOTE]
>Соединитель Dynamics, используемый для необязательного свойства organizationName, чтобы идентифицировать экземпляр Dynamics CRM или 365 Online. Во время работы соединителя вам предлагается вместо этого указать новое свойство serviceUri, чтобы повысить производительность обнаружения экземпляра.

**Пример: Dynamics Online с использованием субъекта-службы AAD + ключ проверки подлинности**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
**Пример: Dynamics Online с использованием субъекта-службы AAD + проверка подлинности сертификата**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

**Пример. Dynamics Online с использованием проверки подлинности Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
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
| Тип | Для свойства Type необходимо задать значение **Dynamics**, **динамикскрм**или **коммондатасервицефораппс**. | ДА |
| deploymentType | Тип развертывания для экземпляра Dynamics. Для Dynamics (локальная версия) с IFD необходимо задать значение **OnPremisesWithIfd**.| ДА |
| hostName | Имя узла локального сервера Dynamics. | ДА |
| порт | Порт локального сервера Dynamics. | Нет, значение по умолчанию — 443 |
| оrganizationName | Имя организации экземпляра Dynamics. | ДА |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Укажите **Ifd** для Dynamics (локальная версия) с IFD. | ДА |
| Имя пользователя | Укажите имя пользователя для подключения к Dynamics. | ДА |
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и передавать его оттуда в действие копирования при фактическом копировании данных. Подробнее это описано в статье [о хранении учетных данных в Key Vault](store-credentials-in-key-vault.md). | ДА |
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

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство Type набора данных должно иметь значение **DynamicsEntity**, **динамикскрментити**или **коммондатасервицефораппсентити**. |ДА |
| entityName | Логическое имя сущности, которое требуется получить. | "Нет" для источника (если свойство query указано в источнике действия), "Да" для приемника |

**Пример.**

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

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство Type источника действия копирования должно иметь значение **DynamicsSource**, **динамикскрмсаурце**или **коммондатасервицефораппссаурце**. | ДА |
| query | FetchXML — это защищаемый язык запросов, используемый в Dynamics (Online и локальная версия). См. указанный ниже пример. Дополнительные сведения см. в статье [Создание запросов с помощью FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | "Нет" (если для набора данных задано свойство entityName) |

>[!NOTE]
>Столбец первичного ключа будет копироваться всегда, даже если он отсутствует в проекции столбца, настроенной в запросе FetchXML.

> [!IMPORTANT]
>- При копировании данных из Dynamics явное сопоставление столбцов из Dynamics с приемником является необязательным, но с высокой повторной назначением для обеспечения детерминированного результата копирования.
>- При импорте схемы в пользовательском интерфейсе ADF определяет схему, выполнив выборку первых строк из результата запроса Dynamics для инициализации списка исходных столбцов. в этом случае столбцы без значений в верхних строках будут опущены. Такое же поведение применяется к выполнению копирования, если нет явного сопоставления. В сопоставление можно проверить и добавить дополнительные столбцы, которые будут учитываться во время выполнения копирования.

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

Чтобы скопировать данные в Dynamics, в разделе **приемника** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство Type приемника действия копирования должно иметь значение **DynamicsSink**, **динамикскрмсинк**или **коммондатасервицефораппссинк**. | ДА |
| writeBehavior | Поведение операции при записи.<br/>Допустимое значение: **Upsert**. | ДА |
| алтернатекэйнаме | Укажите альтернативное имя ключа, определенное в сущности, для выполнения "Upsert". | Нет |
| writeBatchSize | Количество строк данных, записываемых в Dynamics в каждом пакете. | Нет (значение по умолчанию — 10) |
| ignoreNullValues | Указывает, следует ли игнорировать значения NULL из входных данных (за исключением ключевых полей) во время операции записи.<br/>Допустимые значения: **true** и **false**.<br>- **True**: при выполнении операции upsert или update оставьте данные в целевом объекте неизменными. При выполнении операции вставки (insert) вставьте определенное значение по умолчанию.<br/>- **False**: при выполнении операции upsert или update обновите данные в целевом объекте до значения NULL. При выполнении операции вставки (insert) вставьте значение NULL. | Нет (по умолчанию используется значение false) |

>[!NOTE]
>Значение по умолчанию для приемника "**writeBatchSize**" и операция копирования " **[parallelCopies](copy-activity-performance.md#parallel-copy)** " для приемника Dynamics равны 10. Таким образом в Dynamics одновременно отправляются 100 записей.

Для Dynamics 365 в сети существует ограничение на [2 одновременных пакетных вызова на организацию](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Если этот предел превышен, возникает ошибка "сервер занят" до того, как будет выполнен первый запрос. Поддержание "writeBatchSize", меньшее или равное 10, позволит избежать такого регулирования количества одновременных вызовов.

Оптимальное сочетание "**writeBatchSize**" и "**parallelCopies**" зависит от схемы сущности, например число столбцов, размер строк, число подключаемых модулей, рабочих процессов и действий рабочих процессов, подключенных к этим вызовам, и т. д. Значение по умолчанию 10 writeBatchSize * 10 parallelCopies является рекомендацией в соответствии со службой Dynamics, которая будет работать для большинства сущностей Dynamics, но может не быть лучшей производительностью. Вы можете настроить производительность путем корректировки комбинации в настройках активности копирования.

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
| AttributeTypeCode.BigInt | Длинные | ✓ | ✓ |
| AttributeTypeCode.Boolean | Логический | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | DOUBLE | ✓ | ✓ |
| AttributeType.EntityName | Строка | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓ (связанный с одним объектом) |
| AttributeType.ManagedProperty | Логический | ✓ | |
| AttributeType.Memo | Строка | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | Строка | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Типы данных Dynamics AttributeType. CalendarRules, AttributeType. Мултиселектпикклист и AttributeType. PartyList не поддерживаются.

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
