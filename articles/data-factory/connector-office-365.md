---
title: Копирование данных из Office 365, с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из Office 365 в поддерживаемые целевые хранилища данных (приемники) с помощью действия копирования в конвейере Фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: jingwang
ms.openlocfilehash: 9ca3cbb1ef46c7fe53b6b16bda40ebef245613f3
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415658"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Копирование данных из Office 365 в Azure с помощью фабрики данных Azure

Фабрика данных Azure позволяет в большом масштабе перемещать в Azure наборы корпоративных данных, хранимые в Office 365, чтобы на основе этих ценных ресурсов данных создавать аналитические приложения и получать важные сведения. Интеграция с Privileged Access Management обеспечивает безопасное управление доступом к ценным и проверенным данным в Office 365.  Дополнительные сведения о подключении Microsoft Graph к данным см. [здесь](https://docs.microsoft.com/graph/data-connect-concept-overview).

Из этой статьи вы узнаете, как с помощью действия копирования в Фабрике данных Azure скопировать данные из Office 365. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Сейчас в пределах отдельного действия копирования вы можете только **копировать данные из Office 365 в [хранилище BLOB-объектов](connector-azure-blob-storage.md), [Gen1 хранилища Озера данных Azure](connector-azure-data-lake-store.md), и [Gen2 хранилища Озера данных Azure ](connector-azure-data-lake-storage.md) в формате JSON** (тип setOfObjects). Если вы хотите загрузить данные Office 365 в хранилища других типов или в других форматах, добавьте к первому действию копирования дополнительные действия, которые передадут эти данные в любое [поддерживаемое ADF целевое хранилище](copy-activity-overview.md#supported-data-stores-and-formats) (см. столбец "Поддерживается в качестве приемника" в таблице "Поддерживаемые хранилища и форматы данных").

>[!IMPORTANT]
>- Подписка Azure, содержащая фабрику данных и целевое хранилище данных должны размещаться в том же клиенте Azure Active Directory (AAD), что и клиент Office 365.
>- Среда выполнения Azure Integration Runtime, используемая для действия копирования, а также целевой объект должны размещаться в том же регионе, где расположен почтовый ящик пользователей клиента Office 365. В [этой статье](concepts-integration-runtime.md#integration-runtime-location) описано, как определяется расположение Azure Integration Runtime. В [этой таблице](https://docs.microsoft.com/graph/data-connect-datasets#regions) собран список поддерживаемых регионов Office и соответствующих им регионов Azure.
>- Проверка подлинности субъекта-службы — единственный механизм проверки подлинности поддерживается для хранилища BLOB-объектов Azure, Gen1 хранилища Озера данных Azure и Gen2 хранилища Озера данных Azure как целевое хранилище.

## <a name="prerequisites"></a>Технические условия

Чтобы скопировать данные из Office 365 в Azure, выполните следующие предварительные требования:

- Администратору клиента Office 365 необходимо выполнить для подключения описанные [здесь](https://docs.microsoft.com/graph/data-connect-get-started) действия.
- Создайте и настройте веб-приложение AAD в Azure Active Directory.  Инструкции см. в разделе [Создание приложения Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Запишите следующие значения, которые вам потребуются для определения связанной службы для Office 365.
    - Идентификатор клиента. Инструкции см. в статье о [получении идентификатора клиента](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
    - Идентификатор приложения и ключ приложения.  Инструкции см. в статье о [получении идентификатора приложения и ключа проверки подлинности](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).
- Добавьте удостоверение пользователя, который будет обращаться к данным в роли владельца веб-приложения AAD (в AAD откройте "Веб-приложения" > "Параметры" > "Владельцы" > "Добавить владельца"). 
    - Удостоверение пользователя должно находиться в организации Office 365, из которой вы получаете данные, и не должно быть удостоверением гостевого пользователя.

## <a name="approving-new-data-access-requests"></a>Утверждение новых запросов на доступ к данным

Если вы впервые запрашиваете данные для определенного контекста (который определяется комбинацией таблицы данных, к которой осуществляется доступ, целевой учетной записи, в которую загружаются данные, и идентификатора пользователя, используемого для запроса доступа к данным), для действия копирования будет отображаться состояние [Выполняется](copy-activity-overview.md#monitoring) и, только когда вы щелкните ссылку "Подробности" в разделе"Действия", вы увидите состояние "RequesetingConsent".  Участник группы лиц, утверждающих доступ к данным, должен утвердить запрос в Privileged Access Management, прежде чем можно будет продолжить извлечение данных.

На [этой странице](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) описано, как утверждающее лицо может утвердить запрос на доступ к данным, а [здесь](https://docs.microsoft.com/graph/data-connect-pam) представлены общие сведения об интеграции с Privileged Access Management и настройке группы лиц, утверждающих доступ к данным.

## <a name="policy-validation"></a>Проверка политики

Если в рамках управляемого приложения создан файл определения приложения, и для ресурсов в группе ресурсов для управления назначены политики Azure, для каждого запуска действия копирования этот файл определения приложения проверяет применение назначенных политик. [Здесь](https://docs.microsoft.com/graph/data-connect-policies#policies) собран список поддерживаемых политик.

## <a name="getting-started"></a>Приступая к работе

>[!TIP]
>Пошаговое руководство по использованию соединителя Office 365 см. в статье [Загрузка данных из Office 365 с помощью Фабрики данных Azure](load-office-365-data.md).

Можно создать конвейер с использованием действия копирования, воспользовавшись одним из приведенных ниже инструментов или пакетов SDK. Щелкните ссылку, чтобы перейти к учебнику, содержащему пошаговые инструкции по созданию конвейера с использованием действия копирования. 

- [портал Azure](quickstart-create-data-factory-portal.md)
- [ПАКЕТ SDK .NET](quickstart-create-data-factory-dot-net.md)
- [Пакет SDK для Python](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Шаблон Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md) 

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей Фабрики данных, относящихся к соединителю Office 365.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Office 365 поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойству type необходимо задать следующее значение: **Office365** | Да |
| office365TenantId | Идентификатор клиента Azure, которому принадлежит учетная запись Office 365. | Да |
| servicePrincipalTenantId | Укажите сведения о клиенте, в котором находится веб-приложение AAD. | Да |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Yes |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. | Да |
| connectVia | Среда Integration Runtime, используемая для подключения к хранилищу данных.  Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Нет |

>[!NOTE]
> Значения **office365TenantId** и **servicePrincipalTenantId** имеют следующие различия и принимают следующие значения.
>- Если вы корпоративный разработчик и создаете приложение для работы с данными Office 365, которое будет использоваться в пределах организации, в обоих свойствах следует указать один и тот же идентификатор клиента, а именно идентификатор AAD вашей организации.
>- Если вы независимый разработчик и разрабатываете приложение для клиентов, office365TenantId будет содержать идентификатор AAD клиента (который будет устанавливать приложение), а servicePrincipalTenantId — идентификатор AAD вашей компании

**Пример.**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Office 365.

Для копирования данных из Office 365 поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type набора данных необходимо задать следующее значение: **Office365Table** | Да |
| tableName | Имя набора данных, который нужно извлечь из Office 365. [Здесь](https://docs.microsoft.com/graph/data-connect-datasets#datasets) вы найдете список наборов данных, доступных для извлечения из Office 365. | Да |
| allowedGroups | Предикат выбора группы.  Оно используется для выбора до 10 групп пользователей, для которых будут извлекаться данные.  Если группы не указаны, данные возвращаются для всей организации. | Нет |
| userScopeFilterUri | Когда `allowedGroups` свойство не указано, можно использовать выражение предиката, которое применяется для всего клиента для фильтрации определенных строк для извлечения из Office 365. Формат предиката должен соответствовать формата запроса API Microsoft Graph, например `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | Нет |
| dateFilterColumn | Имя столбца фильтр даты и времени. Используйте это свойство, чтобы ограничить диапазон времени, для которого Office 365 извлекаются данные. | Да, если набор данных содержит один или несколько столбцов даты и времени. См. [здесь](https://docs.microsoft.com/graph/data-connect-filtering#filtering) для список наборов данных, который требует этот фильтр даты и времени. |
| startTime | Начальное значение даты и времени, следует выполнить фильтрацию. | Да, в том случае, если `dateFilterColumn` указан |
| endTime | Конечное значение даты и времени следует выполнить фильтрацию. | Да, в том случае, если `dateFilterColumn` указан |

**Пример**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "Id",
                "type": "String",
                "description": "The unique identifier of the event."
            },
            {
                "name": "CreatedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was created."
            },
            {
                "name": "LastModifiedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was last modified."
            },
            {
                "name": "ChangeKey",
                "type": "String",
                "description": "Identifies the version of the event object. Every time the event is changed, ChangeKey changes as well. This allows Exchange to apply changes to the correct version of the object."
            },
            {
                "name": "Categories",
                "type": "String",
                "description": "The categories associated with the event. Format: ARRAY<STRING>"
            },
            {
                "name": "OriginalStartTimeZone",
                "type": "String",
                "description": "The start time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "OriginalEndTimeZone",
                "type": "String",
                "description": "The end time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "ResponseStatus",
                "type": "String",
                "description": "Indicates the type of response sent in response to an event message. Format: STRUCT<Response: STRING, Time: STRING>"
            },
            {
                "name": "iCalUId",
                "type": "String",
                "description": "A unique identifier that is shared by all instances of an event across different calendars."
            },
            {
                "name": "ReminderMinutesBeforeStart",
                "type": "Int32",
                "description": "The number of minutes before the event start time that the reminder alert occurs."
            },
            {
                "name": "IsReminderOn",
                "type": "Boolean",
                "description": "Set to true if an alert is set to remind the user of the event."
            },
            {
                "name": "HasAttachments",
                "type": "Boolean",
                "description": "Set to true if the event has attachments."
            },
            {
                "name": "Subject",
                "type": "String",
                "description": "The text of the event's subject line."
            },
            {
                "name": "Body",
                "type": "String",
                "description": "The body of the message associated with the event.Format: STRUCT<ContentType: STRING, Content: STRING>"
            },
            {
                "name": "Importance",
                "type": "String",
                "description": "The importance of the event: Low, Normal, High."
            },
            {
                "name": "Sensitivity",
                "type": "String",
                "description": "Indicates the level of privacy for the event: Normal, Personal, Private, Confidential."
            },
            {
                "name": "Start",
                "type": "String",
                "description": "The start time of the event. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "End",
                "type": "String",
                "description": "The date and time that the event ends. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "Location",
                "type": "String",
                "description": "Location information of the event. Format: STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>>"
            },
            {
                "name": "IsAllDay",
                "type": "Boolean",
                "description": "Set to true if the event lasts all day. Adjusting this property requires adjusting the Start and End properties of the event as well."
            },
            {
                "name": "IsCancelled",
                "type": "Boolean",
                "description": "Set to true if the event has been canceled."
            },
            {
                "name": "IsOrganizer",
                "type": "Boolean",
                "description": "Set to true if the message sender is also the organizer."
            },
            {
                "name": "Recurrence",
                "type": "String",
                "description": "The recurrence pattern for the event. Format: STRUCT<Pattern: STRUCT<Type: STRING, `Interval`: INT, Month: INT, DayOfMonth: INT, DaysOfWeek: ARRAY<STRING>, FirstDayOfWeek: STRING, Index: STRING>, `Range`: STRUCT<Type: STRING, StartDate: STRING, EndDate: STRING, RecurrenceTimeZone: STRING, NumberOfOccurrences: INT>>"
            },
            {
                "name": "ResponseRequested",
                "type": "Boolean",
                "description": "Set to true if the sender would like a response when the event is accepted or declined."
            },
            {
                "name": "ShowAs",
                "type": "String",
                "description": "The status to show: Free, Tentative, Busy, Oof, WorkingElsewhere, Unknown."
            },
            {
                "name": "Type",
                "type": "String",
                "description": "The event type: SingleInstance, Occurrence, Exception, SeriesMaster."
            },
            {
                "name": "Attendees",
                "type": "String",
                "description": "The collection of attendees for the event. Format: ARRAY<STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>, Status: STRUCT<Response: STRING, Time: STRING>, Type: STRING>>"
            },
            {
                "name": "Organizer",
                "type": "String",
                "description": "The organizer of the event. Format: STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>>"
            },
            {
                "name": "WebLink",
                "type": "String",
                "description": "The URL to open the event in Outlook Web App."
            },
            {
                "name": "Attachments",
                "type": "String",
                "description": "The FileAttachment and ItemAttachment attachments for the message. Navigation property. Format: ARRAY<STRUCT<LastModifiedDateTime: STRING, Name: STRING, ContentType: STRING, Size: INT, IsInline: BOOLEAN, Id: STRING>>"
            },
            {
                "name": "BodyPreview",
                "type": "String",
                "description": "The preview of the message associated with the event. It is in text format."
            },
            {
                "name": "Locations",
                "type": "String",
                "description": "The locations where the event is held or attended from. The location and locations properties always correspond with each other. Format:  ARRAY<STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>, LocationEmailAddress: STRING, LocationUri: STRING, LocationType: STRING, UniqueId: STRING, UniqueIdType: STRING>>"
            },
            {
                "name": "OnlineMeetingUrl",
                "type": "String",
                "description": "A URL for an online meeting. The property is set only when an organizer specifies an event as an online meeting such as a Skype meeting"
            },
            {
                "name": "OriginalStart",
                "type": "DateTime",
                "description": "The start time that was set when the event was created in UTC time."
            },
            {
                "name": "SeriesMasterId",
                "type": "String",
                "description": "The ID for the recurring series master item, if this event is part of a recurring series."
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1",
            "dateFilterColumn": "CreatedDateTime",
            "startTime": "2019-04-28T16:00:00.000Z",
            "endTime": "2019-05-05T16:00:00.000Z",
            "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником данных Office 365.

### <a name="office-365-as-source"></a>Office 365 в качестве источника

Чтобы копировать данные из Office 365, установите для действия копирования тип источника **Office365Source**. В разделе **source** для действия копирования другие свойства не поддерживаются.

**Пример.**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
