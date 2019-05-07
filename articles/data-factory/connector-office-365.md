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
ms.date: 05/06/2019
ms.author: jingwang
ms.openlocfilehash: 8b8ffeb505f7d0978a736190b3d38c0246a38ebc
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145003"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory-preview"></a>Копирование данных из Office 365 с помощью Фабрики данных Azure (предварительная версия) 

Фабрика данных Azure позволяет в большом масштабе перемещать в Azure наборы корпоративных данных, хранимые в Office 365, чтобы на основе этих ценных ресурсов данных создавать аналитические приложения и получать важные сведения. Интеграция с Privileged Access Management обеспечивает безопасное управление доступом к ценным и проверенным данным в Office 365.  Дополнительные сведения о подключении Microsoft Graph к данным см. [здесь](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki).

Из этой статьи вы узнаете, как с помощью действия копирования в Фабрике данных Azure скопировать данные из Office 365. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Сейчас в пределах отдельного действия копирования вы можете только **копировать данные из Office 365 в [хранилище BLOB-объектов](connector-azure-blob-storage.md), [Gen1 хранилища Озера данных Azure](connector-azure-data-lake-store.md), и [Gen2 хранилища Озера данных Azure ](connector-azure-data-lake-storage.md) в формате JSON** (тип setOfObjects). Если вы хотите загрузить данные Office 365 в хранилища других типов или в других форматах, добавьте к первому действию копирования дополнительные действия, которые передадут эти данные в любое [поддерживаемое ADF целевое хранилище](copy-activity-overview.md#supported-data-stores-and-formats) (см. столбец "Поддерживается в качестве приемника" в таблице "Поддерживаемые хранилища и форматы данных").

>[!IMPORTANT]
>- Подписка Azure, содержащая фабрику данных и целевое хранилище данных должны размещаться в том же клиенте Azure Active Directory (AAD), что и клиент Office 365.
>- Среда выполнения Azure Integration Runtime, используемая для действия копирования, а также целевой объект должны размещаться в том же регионе, где расположен почтовый ящик пользователей клиента Office 365. В [этой статье](concepts-integration-runtime.md#integration-runtime-location) описано, как определяется расположение Azure Integration Runtime. В [этой таблице](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#data-regions) собран список поддерживаемых регионов Office и соответствующих им регионов Azure.
>-  Если вы загружаете данные из Office 365 в **хранилище BLOB-объектов Azure**, при определении связанных служб для хранилища BLOB-объектов Azure обязательно используйте **[аутентификацию субъекта-службы](connector-azure-blob-storage.md#service-principal-authentication)**, а не [ключ учетной записи](connector-azure-blob-storage.md#account-key-authentication), [подписанные URL-адреса](connector-azure-blob-storage.md#shared-access-signature-authentication) или [управляемые удостоверения для ресурсов Azure](connector-azure-blob-storage.md#managed-identity).
>-  Если вы загружаете данные из Office 365 в **Azure Data Lake Storage 1-го поколения**, при определении связанных служб для Azure Data Lake Storage 1-го поколения обязательно используйте [**аутентификацию субъекта-службы**](connector-azure-data-lake-store.md#use-service-principal-authentication), а не [управляемые удостоверения для ресурсов Azure](connector-azure-data-lake-store.md#managed-identity).

## <a name="prerequisites"></a>Технические условия

Чтобы скопировать данные из Office 365 в Azure, выполните следующие предварительные требования:

- Администратору клиента Office 365 необходимо выполнить для подключения описанные [здесь](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding) действия.
- Создайте и настройте веб-приложение AAD в Azure Active Directory.  Инструкции см. в разделе [Создание приложения Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Запишите следующие значения, которые вам потребуются для определения связанной службы для Office 365.
    - Идентификатор клиента. Инструкции см. в статье о [получении идентификатора клиента](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
    - Идентификатор приложения и ключ приложения.  Инструкции см. в статье о [получении идентификатора приложения и ключа проверки подлинности](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).
- Добавьте удостоверение пользователя, который будет обращаться к данным в роли владельца веб-приложения AAD (в AAD откройте "Веб-приложения" > "Параметры" > "Владельцы" > "Добавить владельца"). 
    - Удостоверение пользователя должно находиться в организации Office 365, из которой вы получаете данные, и не должно быть удостоверением гостевого пользователя.

## <a name="approving-new-data-access-requests"></a>Утверждение новых запросов на доступ к данным

Если вы впервые запрашиваете данные для определенного контекста (который определяется комбинацией таблицы данных, к которой осуществляется доступ, целевой учетной записи, в которую загружаются данные, и идентификатора пользователя, используемого для запроса доступа к данным), для действия копирования будет отображаться состояние [Выполняется](copy-activity-overview.md#monitoring) и, только когда вы щелкните ссылку "Подробности" в разделе"Действия", вы увидите состояние "RequesetingConsent".  Участник группы лиц, утверждающих доступ к данным, должен утвердить запрос в Privileged Access Management, прежде чем можно будет продолжить извлечение данных.

На [этой странице](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Approving-data-access-requests) описано, как утверждающее лицо может утвердить запрос на доступ к данным, а [здесь](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#integration-with-privileged-access-management) представлены общие сведения об интеграции с Privileged Access Management и настройке группы лиц, утверждающих доступ к данным.

## <a name="policy-validation"></a>Проверка политики

Если в рамках управляемого приложения создан файл определения приложения, и для ресурсов в группе ресурсов для управления назначены политики Azure, для каждого запуска действия копирования этот файл определения приложения проверяет применение назначенных политик. [Здесь](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#policies) собран список поддерживаемых политик.

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
| type | Свойству type необходимо задать следующее значение: **Office365** | Yes |
| office365TenantId | Идентификатор клиента Azure, которому принадлежит учетная запись Office 365. | Yes |
| servicePrincipalTenantId | Укажите сведения о клиенте, в котором находится веб-приложение AAD. | Yes |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Yes |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. | Yes |
| connectVia | Среда Integration Runtime, используемая для подключения к хранилищу данных.  Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Нет  |

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
| type | Для свойства type набора данных необходимо задать следующее значение: **Office365Table** | Yes |
| tableName | Имя набора данных, который нужно извлечь из Office 365. [Здесь](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#datasets) вы найдете список наборов данных, доступных для извлечения из Office 365. | Yes |
| predicate | Выражение предиката, которое позволяет фильтровать определенные строки при извлечении данных из Office 365.  В [этой статье](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#filters) указано, какие столбцы можно использовать в предикате фильтрации для каждой таблицы, и описан формат выражения фильтра. | Нет <br>(Если предиката не указан, по умолчанию настраивается извлечение данных за последние 30 дней.) |

**Пример**

```json
{
    "name": "Office365Table1",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "ReceivedDateTime",
                "type": "datetime"
            },
            {
                "name": "SentDateTime",
                "type": "datetime"
            },
            {
                "name": "HasAttachments",
                "type": "boolean"
            },
            {
                "name": "InternetMessageId",
                "type": "string"
            },
            {
                "name": "Subject",
                "type": "string"
            },
            {
                "name": "Importance",
                "type": "string"
            },
            {
                "name": "ParentFolderId",
                "type": "string"
            },
            {
                "name": "Sender",
                "type": "string"
            },
            {
                "name": "From",
                "type": "string"
            },
            {
                "name": "ToRecipients",
                "type": "string"
            },
            {
                "name": "CcRecipients",
                "type": "string"
            },
            {
                "name": "BccRecipients",
                "type": "string"
            },
            {
                "name": "ReplyTo",
                "type": "string"
            },
            {
                "name": "ConversationId",
                "type": "string"
            },
            {
                "name": "UniqueBody",
                "type": "string"
            },
            {
                "name": "IsDeliveryReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsReadReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsRead",
                "type": "boolean"
            },
            {
                "name": "IsDraft",
                "type": "boolean"
            },
            {
                "name": "WebLink",
                "type": "string"
            },
            {
                "name": "CreatedDateTime",
                "type": "datetime"
            },
            {
                "name": "LastModifiedDateTime",
                "type": "datetime"
            },
            {
                "name": "ChangeKey",
                "type": "string"
            },
            {
                "name": "Categories",
                "type": "string"
            },
            {
                "name": "Id",
                "type": "string"
            },
            {
                "name": "Attachments",
                "type": "string"
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Contact_v0",
            "predicate": "CreatedDateTime >= 2018-07-11T16:00:00.000Z AND CreatedDateTime <= 2018-07-18T16:00:00.000Z"
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
