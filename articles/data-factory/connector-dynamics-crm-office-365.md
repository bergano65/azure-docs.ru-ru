---
title: Копирование данных в динамике (Общая служба данных)
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
ms.openlocfilehash: c891cb4eca2c286b3ac636e5995714accd591772
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417347"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Копирование данных из Dynamics 365 (Common Data Service) или Dynamics CRM и в эти решения с помощью фабрики данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Microsoft Dynamics 365 или Microsoft Dynamics CRM и обратно. Он основан на статье [обзора копирования активности,](copy-activity-overview.md) в представленной общим обзором деятельности copy.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Данные из Dynamics 365 (Common Data Service) или Dynamics CRM можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. В свою очередь, данные из любого хранилища данных, поддерживаемого в качестве источника, можно скопировать в Dynamics 365 (Common Data Service) или Dynamics CRM. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот разъем Dynamics поддерживает версию Dynamics 7.x до 9.x как для онлайн, так и для местных. В частности

- Версия 7.x Карты для Динамики CRM 2015
- Карты версии 8.x к Dynamics CRM 2016 и ранней версии Dynamics 365
- Карты версии 9.x к более поздней версии Dynamics 365

Обратитесь к следующей таблице, наиболее поддерживаемой типы и конфигурации проверки подлинности для соответствующих версий/продуктов Dynamics. (IFD — сокращение от термина "развертывание с выходом в Интернет".)

| Версии Dynamics | Типы проверки подлинности | Примеры связанной службы |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 Online <br> Dynamics CRM Online | Директор службы AAD <br> Office365 | [Динамика онлайн - aAD-сервис или Office365 auth](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 (локальная версия) с IFD <br> Dynamics CRM 2016 (локальная версия) с IFD <br> Dynamics CRM 2015 (локальная версия) с IFD | IFD | [Динамика на территории с IFD и IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

В частности, для Dynamics 365 поддерживаются следующие типы приложений:

- Dynamics 365 for Sales;
- Dynamics 365 for Customer Service;
- Dynamics 365 for Field Service;
- Dynamics 365 for Project Service Automation;
- Dynamics 365 for Marketing.

Другие типы приложений (например, Dynamics 365 for Finance and Operations, Dynamics 365 for Talent и т. д.) не поддерживаются в этом соединителе.

Этот разъем Dynamics построен поверх [инструмента Dynamics XRM.](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)

>[!TIP]
>[Соединитель Dynamics AX](connector-dynamics-ax.md) также можно использовать для копирования данных из **Dynamics 365 Finance and Operations**.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для Dynamics.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Dynamics поддерживаются следующие свойства:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 и Dynamics CRM Online

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа должно быть настроено на **Dynamics,** **DynamicsCrm**или **CommonDataServiceForApps.** | Да |
| deploymentType | Тип развертывания для экземпляра Dynamics. Должен иметь значение **Online** для Dynamics Online. | Да |
| serviceUri | URL-адрес вашего экземпляра службы Dynamics, например `https://adfdynamics.crm.dynamics.com`. | Да |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Разрешенные значения: **AADServicePrincipal** или **"Office365"**. | Да |
| servicePrincipalId | Укажите идентификатор клиента приложения Azure Active Directory. | Да при `AADServicePrincipal` использовании аутентификации |
| сервисPrincipalCredentialType | Укажите тип учетных данных для использования при аутентификации субъекта-службы. Разрешенные значения: **ServicePrincipalKey** или **ServicePrincipalCert**. | Да при `AADServicePrincipal` использовании аутентификации |
| сервисPrincipalCredential | Укажите основные учетные данные службы. <br>При `ServicePrincipalKey` использовании в `servicePrincipalCredential` качестве типа учетных данных может быть строка (ADF будет шифровать его при развертывании связанных служб) или ссылка на секрет в AKV. <br>При `ServicePrincipalCert` использовании `servicePrincipalCredential` в качестве учетных данных, должна быть ссылка на сертификат в AKV. | Да при `AADServicePrincipal` использовании аутентификации | 
| username | Укажите имя пользователя для подключения к Dynamics. | Да при `Office365` использовании аутентификации |
| password | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да при `Office365` использовании аутентификации |
| connectVia | [Время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника, если связанная с источником служба не имеет среды выполнения интеграции |

>[!NOTE]
>Соединитель Dynamics, используемый для необязательного свойства organizationName, чтобы идентифицировать экземпляр Dynamics CRM или 365 Online. Во время работы соединителя вам предлагается вместо этого указать новое свойство serviceUri, чтобы повысить производительность обнаружения экземпляра.

**Пример: Динамика онлайн с помощью основного сервиса AAD - ключевая аутентификация**

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
**Пример: Динамика онлайн с помощью основного сервиса AAD и проверки подлинности сертификата**

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

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа должно быть настроено на **Dynamics,** **DynamicsCrm**или **CommonDataServiceForApps.** | Да |
| deploymentType | Тип развертывания для экземпляра Dynamics. Для Dynamics (локальная версия) с IFD необходимо задать значение **OnPremisesWithIfd**.| Да |
| hostName | Имя узла локального сервера Dynamics. | Да |
| порт | Порт локального сервера Dynamics. | Нет, значение по умолчанию — 443 |
| оrganizationName | Имя организации экземпляра Dynamics. | Да |
| authenticationType | Тип проверки подлинности для подключения к серверу Dynamics. Укажите **Ifd** для Dynamics (локальная версия) с IFD. | Да |
| username | Укажите имя пользователя для подключения к Dynamics. | Да |
| password | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и передавать его оттуда в действие копирования при фактическом копировании данных. Подробнее это описано в статье [о хранении учетных данных в Key Vault](store-credentials-in-key-vault.md). | Да |
| connectVia | [Время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника |

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

Для копирования данных из динамики и в динамику поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено на **DynamicsEntity,** **DynamicsCrmEntity**или **CommonDataServiceForAppsEntity.** |Да |
| entityName | Логическое имя сущности, которое требуется получить. | "Нет" для источника (если свойство query указано в источнике действия), "Да" для приемника |

**Примере:**

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

Для копирования данных из Dynamics в разделе **источника активности** копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования должно быть установлено на **DynamicsSource,** **DynamicsCrmSource**или **CommonDataServiceForAppsSource.** | Да |
| query | FetchXML — это защищаемый язык запросов, используемый в Dynamics (Online и локальная версия). См. указанный ниже пример. Чтобы узнать больше, смотрите [запросы сборки с помощью FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | "Нет" (если для набора данных задано свойство entityName) |

>[!NOTE]
>Столбец первичного ключа будет копироваться всегда, даже если он отсутствует в проекции столбца, настроенной в запросе FetchXML.

> [!IMPORTANT]
>- При копировании данных из Dynamics явное отображение столбца от Dynamics к раковине является необязательным, но очень переподстроено для обеспечения детерминированного результата копирования.
>- При импорте схемы в авторском uI ADF выводит схему, пробуя верхние строки из результата запроса Dynamics для инициализации списка столбцов исхода, и в этом случае столбцы без значений в верхних рядах будут опущены. То же самое относится и к копированию исполнений, если нет явного отображения. Вы можете просмотреть и добавить больше столбцов в отображение, которое будет соблюдаться во время выполнения копий.

**Примере:**

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

Для копирования данных в Dynamics в разделе **«Поглотитель активности** копирования» поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа раковины активности копирования должно быть установлено на **DynamicsSink,** **DynamicsCrmSink**, или **CommonDataServiceForAppsSink.** | Да |
| writeBehavior | Поведение операции при записи.<br/>Разрешенное значение **"Upsert"**. | Да |
| alternateKeyName | Укажите альтернативное ключевое имя, определенное на объекте для выполнения "Upsert". | нет |
| writeBatchSize | Количество строк данных, записываемых в Dynamics в каждом пакете. | Нет (значение по умолчанию — 10) |
| ignoreNullValues | Указывает, следует ли игнорировать значения NULL из входных данных (за исключением ключевых полей) во время операции записи.<br/>Допустимые значения: **true** и **false**.<br>- **True**: при выполнении операции upsert или update оставьте данные в целевом объекте неизменными. При выполнении операции вставки (insert) вставьте определенное значение по умолчанию.<br/>- **False**: при выполнении операции upsert или update обновите данные в целевом объекте до значения NULL. При выполнении операции вставки (insert) вставьте значение NULL. | Нет (по умолчанию используется значение false) |

>[!NOTE]
>Значение по умолчанию раковины «**writeBatchSize**« и активность копирования «**[parallelCopies](copy-activity-performance-features.md#parallel-copy)**» для раковины Динамики оба 10. Таким образом в Dynamics одновременно отправляются 100 записей.

Для Dynamics 365 в сети существует ограничение на [2 одновременных пакетных вызова на организацию](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Если этот предел превышен, возникает ошибка "сервер занят" до того, как будет выполнен первый запрос. Поддержание "writeBatchSize", меньшее или равное 10, позволит избежать такого регулирования количества одновременных вызовов.

Оптимальное сочетание **"writeBatchSize"** и **"parallelCopies"** зависит от схемы вашего объекта, например, от количества столбцов, размера строки, количества плагинов/рабочих процессов/рабочих процессов, подключенных к этим вызовам и т.д. Настройка по умолчанию 10 writeBatchSize и 10 parallelCopies является рекомендацией в соответствии с службой Dynamics, которая будет работать для большинства объектов Dynamics, хотя может быть не лучшей производительностью. Вы можете настроить производительность путем корректировки комбинации в настройках активности копирования.

**Примере:**

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
| AttributeTypeCode.Boolean | Логическое | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Строка | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (связанный с одним объектом) |
| AttributeType.ManagedProperty | Логическое | ✓ | |
| AttributeType.Memo | Строка | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | Строка | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Типы данных Dynamics AttributeType.CalendarRules, AttributeType.MultiSelectPicklist и AttributeType.PartyList не поддерживаются.

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
