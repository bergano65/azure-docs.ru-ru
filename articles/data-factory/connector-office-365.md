---
title: Копирование данных из Office 365 с помощью фабрики данных Azure
description: Узнайте, как копировать данные из Office 365 в поддерживаемые целевые хранилища данных (приемники) с помощью действия копирования в конвейере Фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: ea68fa8d9326e6d9ebb4f475d16ac83959cae6e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416876"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Копирование данных из Office 365 в Azure с помощью Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory интегрируется с [данными Microsoft Graph,](https://docs.microsoft.com/graph/data-connect-concept-overview)что позволяет выносить богатые организационные данные в вашем арендаторе Office 365 в Azure масштабируемым способом и создавать аналитические приложения и извлекать информацию на основе этих ценных ресурсов данных. Интеграция с Privileged Access Management обеспечивает безопасное управление доступом к ценным и проверенным данным в Office 365.  Пожалуйста, обратитесь к [этой ссылке](https://docs.microsoft.com/graph/data-connect-concept-overview) для обзора данных Microsoft Graph, которые подключаются к [этой ссылке](https://docs.microsoft.com/graph/data-connect-policies#licensing) для получения информации о лицензировании.

Из этой статьи вы узнаете, как с помощью действия копирования в Фабрике данных Azure скопировать данные из Office 365. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности
Разъем ADF Office 365 и подключение данных Microsoft Graph позволяют в масштабе проглатывать различные типы наборов данных из почтовых ящиков Exchange Email, включая контакты адресной книги, события календаря, сообщения электронной почты, информацию о пользователе, настройки почтового ящика и так далее.  Обратитесь [с](https://docs.microsoft.com/graph/data-connect-datasets) полным списком доступных наборов данных.

На данный момент в рамках действия с одной копией можно **скопировать данные из Office 365 в [хранилище Azure Blob,](connector-azure-blob-storage.md) [хранилище Azure Data Lake Data Gen1](connector-azure-data-lake-store.md)и [хранилище Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) в формате JSON** (тип setOfObjects). Если вы хотите загрузить данные Office 365 в хранилища других типов или в других форматах, добавьте к первому действию копирования дополнительные действия, которые передадут эти данные в любое [поддерживаемое ADF целевое хранилище](copy-activity-overview.md#supported-data-stores-and-formats) (см. столбец "Поддерживается в качестве приемника" в таблице "Поддерживаемые хранилища и форматы данных").

>[!IMPORTANT]
>- Подписка Azure, содержащая фабрику данных и целевое хранилище данных должны размещаться в том же клиенте Azure Active Directory (AAD), что и клиент Office 365.
>- Среда выполнения Azure Integration Runtime, используемая для действия копирования, а также целевой объект должны размещаться в том же регионе, где расположен почтовый ящик пользователей клиента Office 365. В [этой статье](concepts-integration-runtime.md#integration-runtime-location) описано, как определяется расположение Azure Integration Runtime. В [этой таблице](https://docs.microsoft.com/graph/data-connect-datasets#regions) собран список поддерживаемых регионов Office и соответствующих им регионов Azure.
>- Основная аутентификация службы — единственный механизм аутентификации, поддерживаемый для хранения Azure Blob Storage, Azure Data Lake Storage Gen1 и Azure Data Lake Storage Gen2 в качестве складов назначения.

## <a name="prerequisites"></a>Предварительные требования

Чтобы скопировать данные из Office 365 в Azure, выполните следующие предварительные требования:

- Администратору клиента Office 365 необходимо выполнить для подключения описанные [здесь](https://docs.microsoft.com/graph/data-connect-get-started) действия.
- Создайте и настройте веб-приложение AAD в Azure Active Directory.  Инструкции см. в разделе [Создание приложения Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Запишите следующие значения, которые вам потребуются для определения связанной службы для Office 365.
    - Идентификатор клиента. Инструкции см. в статье о [получении идентификатора клиента](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - Идентификатор приложения и ключ приложения.  Инструкции см. в статье о [получении идентификатора приложения и ключа проверки подлинности](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Добавьте удостоверение пользователя, который будет обращаться к данным в роли владельца веб-приложения AAD (в AAD откройте "Веб-приложения" > "Параметры" > "Владельцы" > "Добавить владельца"). 
    - Удостоверение пользователя должно находиться в организации Office 365, из которой вы получаете данные, и не должно быть удостоверением гостевого пользователя.

## <a name="approving-new-data-access-requests"></a>Утверждение новых запросов на доступ к данным

Если вы впервые запрашиваете данные для определенного контекста (который определяется комбинацией таблицы данных, к которой осуществляется доступ, целевой учетной записи, в которую загружаются данные, и идентификатора пользователя, используемого для запроса доступа к данным), для действия копирования будет отображаться состояние [Выполняется](copy-activity-overview.md#monitoring) и, только когда вы щелкните ссылку "Подробности" в разделе"Действия", вы увидите состояние "RequesetingConsent".  Участник группы лиц, утверждающих доступ к данным, должен утвердить запрос в Privileged Access Management, прежде чем можно будет продолжить извлечение данных.

На [этой странице](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) описано, как утверждающее лицо может утвердить запрос на доступ к данным, а [здесь](https://docs.microsoft.com/graph/data-connect-pam) представлены общие сведения об интеграции с Privileged Access Management и настройке группы лиц, утверждающих доступ к данным.

## <a name="policy-validation"></a>Проверка политики

Если в рамках управляемого приложения создан файл определения приложения, и для ресурсов в группе ресурсов для управления назначены политики Azure, для каждого запуска действия копирования этот файл определения приложения проверяет применение назначенных политик. [Здесь](https://docs.microsoft.com/graph/data-connect-policies#policies) собран список поддерживаемых политик.

## <a name="getting-started"></a>Начало работы

>[!TIP]
>Пошаговое руководство по использованию соединителя Office 365 см. в статье [Загрузка данных из Office 365 с помощью Фабрики данных Azure](load-office-365-data.md).

Можно создать конвейер с использованием действия копирования, воспользовавшись одним из приведенных ниже инструментов или пакетов SDK. Щелкните ссылку, чтобы перейти к учебнику, содержащему пошаговые инструкции по созданию конвейера с использованием действия копирования. 

- [Портал Azure](quickstart-create-data-factory-portal.md)
- [Пакет SDK для .NET](quickstart-create-data-factory-dot-net.md)
- [Пакет SDK для Python](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Шаблон менеджера ресурсов Azure](quickstart-create-data-factory-resource-manager-template.md). 

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей Фабрики данных, относящихся к соединителю Office 365.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Office 365 поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Office365**. | Да |
| office365TenantId | Идентификатор клиента Azure, которому принадлежит учетная запись Office 365. | Да |
| servicePrincipalTenantId | Укажите сведения о клиенте, в котором находится веб-приложение AAD. | Да |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Да |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. | Да |
| connectVia | Среда Integration Runtime, используемая для подключения к хранилищу данных.  Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | нет |

>[!NOTE]
> Значения **office365TenantId** и **servicePrincipalTenantId** имеют следующие различия и принимают следующие значения.
>- Если вы корпоративный разработчик и создаете приложение для работы с данными Office 365, которое будет использоваться в пределах организации, в обоих свойствах следует указать один и тот же идентификатор клиента, а именно идентификатор AAD вашей организации.
>- Если вы независимый разработчик и разрабатываете приложение для клиентов, office365TenantId будет содержать идентификатор AAD клиента (который будет устанавливать приложение), а servicePrincipalTenantId — идентификатор AAD вашей компании

**Примере:**

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

Полный список разделов и свойств, доступных [datasets](concepts-datasets-linked-services.md) для определения наборов данных, см. В этом разделе содержится список свойств, поддерживаемых набором данных Office 365.

Для копирования данных из Office 365 поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **Office365Table**. | Да |
| tableName | Имя набора данных, который нужно извлечь из Office 365. [Здесь](https://docs.microsoft.com/graph/data-connect-datasets#datasets) вы найдете список наборов данных, доступных для извлечения из Office 365. | Да |

Если вы `dateFilterColumn` `startTime`устанавливали, , `endTime`и `userScopeFilterUri` в наборе данных, он по-прежнему поддерживается как есть, в то время как вам предлагается использовать новую модель в источнике активности в будущем.

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
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником данных Office 365.

### <a name="office-365-as-source"></a>Office 365 в качестве источника

Для копирования данных из Office 365 в разделе **источника активности** копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника копирования должна быть установлена на: **Office365Источник** | Да |
| разрешенныеГруппы | Предикат группового отбора.  Используйте это свойство, чтобы выбрать до 10 групп пользователей, для которых данные будут получены.  Если группы не указаны, данные будут возвращены для всей организации. | нет |
| userScopeFilteruri | Когда `allowedGroups` свойство не указано, можно использовать выражение предикатов, которое применяется ко всему арендатору для фильтрации определенных строк для извлечения из Office 365. Формат предиката должен соответствовать формату запроса AA Microsoft `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`Graph, например. | нет |
| dateFilterColumn | Название столбца фильтра DateTime. Используйте это свойство, чтобы ограничить временной диапазон, для которого извлекаются данные Office 365. | Да, если набор данных имеет один или несколько столбцов DateTime. Обратитесь [за](https://docs.microsoft.com/graph/data-connect-filtering#filtering) списком наборов данных, требующих этого фильтра DateTime. |
| startTime | Начало значения DateTime для фильтрации. | Да, `dateFilterColumn` если указано |
| endTime | Значение End DateTime для фильтрации. | Да, `dateFilterColumn` если указано |
| выходНыеКолонки | Массив столбцов для копирования, чтобы утонуть. | нет |

**Примере:**

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
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
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
