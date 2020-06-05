---
title: Копирование и преобразование данных в хранилище больших двоичных объектов Azure
description: Узнайте, как копировать данные в хранилище больших двоичных объектов и из него, а затем преобразовывать данные в хранилище с помощью фабрики данных.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: ad24f8db2f8f212ebcccf661241016b438ec730c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655391"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Копирование данных в хранилище больших двоичных объектов Azure и обратно с помощью фабрики данных Azure

> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-azure-blob-connector.md)
> * [Текущая версия](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье описано, как использовать действие копирования в Фабрике данных Azure, чтобы копировать данные в хранилище больших двоичных объектов Azure и из него, а также использовать Поток данных для преобразования данных в хранилище больших двоичных объектов Azure. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

>[!TIP]
>Дополнительные сведения о сценарии миграции озера данных или хранилища данных см. в статье [Использование фабрики данных Azure для переноса данных из озера данных или хранилища данных в Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель больших двоичных объектов Azure предназначен для поддержки следующих действий.

- [Действие копирования](copy-activity-overview.md) с использованием [таблицы поддерживаемых источников/приемников](copy-activity-overview.md)
- [Поток данных для сопоставления](concepts-data-flow-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
- [Действие удаления](delete-activity.md)

Для действия копирования этот соединитель хранилища больших двоичных объектов поддерживает следующее.

- Копирование больших двоичных объектов в учетные записи хранения Azure общего назначения, в хранилище горячих и холодных BLOB-объектов и из них. 
- Копирование BLOB-объектов с помощью ключа учетной записи, проверка подлинности ресурсов Azure на основе подписанного URL-адреса, субъект-службы или управляемых удостоверений.
- Копирование больших двоичных объектов из блочных, добавочных или страничных BLOB-объектов, а также копирование данных только в блочные BLOB-объекты.
- Копирование больших двоичных объектов "как есть", анализ или создание больших двоичных объектов путем использования [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).
- [Сохранение метаданных файла во время копирования](#preserve-metadata-during-copy).

>[!IMPORTANT]
>Если включить параметр **Разрешить доверенным службам Майкрософт доступ к этой учетной записи хранения** в настройках брандмауэра службы хранилища Azure, то при попытке использовать среду выполнения интеграции Azure для подключения к хранилищу больших двоичных объектов потребуется использовать [проверку подлинности с использованием управляемого удостоверения](#managed-identity).

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для хранилища BLOB-объектов.

## <a name="linked-service-properties"></a>Свойства связанной службы

Дополнительные сведения о типах аутентификации, поддерживаемых соединителем больших двоичных объектов Azure, см. в следующих разделах.

- [Проверка подлинности на основе ключа учетной записи](#account-key-authentication)
- [Аутентификация SAS](#shared-access-signature-authentication)
- [Проверка подлинности субъекта-службы](#service-principal-authentication)
- [Проверка подлинности на основе управляемого удостоверения службы](#managed-identity)

>[!NOTE]
>При использовании Polybase для загрузки данных в хранилище данных SQL, если в исходном или промежуточном хранилище больших двоичных объектов настроена конечная точка виртуальной сети, необходимо использовать проверку подлинности с использованием управляемого удостоверения в соответствии с требованиями Polybase и использовать локальную среду выполнения интеграции с версией 3.18 или более поздней. Дополнительные предварительные требования к конфигурации см. в разделе [Проверка подлинности с использованием управляемого удостоверения](#managed-identity).

>[!NOTE]
>HDInsights и Машинное обучение Azure поддерживают только проверку подлинности ключа учетной записи хранилища больших двоичных объектов Azure.

### <a name="account-key-authentication"></a>Проверка подлинности на основе ключа учетной записи

При использовании проверки подлинности на основе ключа учетной записи поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойству type должно быть присвоено значение **AzureBlobStorage** (рекомендуется) или **AzureStorage** (см. примечания ниже). |Да |
| connectionString | В свойстве connectionString указываются сведения, необходимые для подключения к службе хранилища. <br/> Вы можете также поместить ключ учетной записи в Azure Key Vault и извлечь конфигурацию `accountKey` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

>[!NOTE]
>Дополнительная конечная точка службы больших двоичных объектов не поддерживается при использовании проверки подлинности ключа учетной записи. Можно использовать другие типы проверки подлинности.

>[!NOTE]
>При использовании связанной службы тип AzureStorage по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый тип связанной службы — AzureBlobStorage.

**Пример**.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример: ключ учетной записи хранения в Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Аутентификация SAS

Подпись общего доступа обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения. Вы можете использовать подписанный URL-адрес, чтобы предоставить клиенту ограниченные разрешения на работу с объектами в вашей учетной записи хранения на определенный период. Не нужно предоставлять совместный доступ к ключам доступа для учетной записи. Подписанный URL-адрес — это универсальный код ресурса (URI), который в своих параметрах запроса содержит все сведения, необходимые для доступа к ресурсу хранилища с прохождением аутентификации. Для доступа к ресурсам хранилища с помощью подписанного URL-адреса клиенту достаточно передать SAS в соответствующий конструктор или метод. Дополнительные сведения о подписанных URL-адресах см. в разделе [Использование подписанных URL-адресов (SAS): Что такое подписанный URL-адрес?](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!NOTE]
>- Сейчас Фабрика данных поддерживает как **подписанные URL-адреса уровня службы**, так и **подписанные URL-адреса уровня учетной записи**. Дополнительные сведения о подписанных URL-адресах см. в статье об [использование подписанных URL-адресов SAS в службе хранилища Azure](../storage/common/storage-sas-overview.md).
>- В более поздней конфигурации набора данных путь к папке — это полный путь, начинающийся с уровня контейнера. Вам нужно настроить такой путь, соответствующий пути в вашем URI SAS.

При использовании аутентификации SAS поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойству type должно быть присвоено значение **AzureBlobStorage** (рекомендуется) или **AzureStorage** (см. примечания ниже). |Да |
| sasUri | Укажите URI подписанного URL-адреса для ресурсов службы хранилища, например для большого двоичного объекта или контейнера. <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы также можете поместить маркер SAS в Azure Key Vault для использования автоматической смены и удалить часть маркера. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

>[!NOTE]
>При использовании связанной службы тип AzureStorage по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый тип связанной службы — AzureBlobStorage.

**Пример**.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример: ключ учетной записи хранения в Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

При создании URI подписанного URL-адреса необходимо учитывать следующее.

- Задайте для объектов соответствующие разрешения на чтение или запись. Они устанавливаются с учетом назначения связанной службы (чтение, запись, чтение и запись) в фабрике данных.
- Задайте **время окончания срока действия** соответствующим образом. Убедитесь, что срок действия доступа к объектам хранилища не истекает в период активности конвейера.
- В зависимости от потребности URI следует создать для нужного контейнера или большого двоичного объекта. URI подписанного URL-адреса для большого двоичного объекта позволяет фабрике данных получить доступ к определенному большому двоичному объекту. URI подписанного URL-адреса для контейнера хранилища BLOB-объектов позволяет Фабрике данных выполнить итерацию по большим двоичным объектам в этом контейнере. Чтобы предоставить доступ к большему или меньшему количеству объектов позднее или обновить URI подписанного URL-адреса, не забудьте обновить связанную службу с помощью нового URI.

### <a name="service-principal-authentication"></a>Проверка подлинности субъекта-службы

Дополнительные сведения о проверке подлинности субъекта-службы хранилища Azure см. в статье [Аутентификация доступа к службе хранилища Azure с помощью Azure Active Directory (предварительная версия)](../storage/common/storage-auth-aad.md).

Чтобы использовать проверку подлинности субъекта-службы, выполните следующие действия.

1. Чтобы зарегистрировать сущность приложения в Azure Active Directory (Azure AD), необходимо следовать указаниям из раздела [Регистрация приложения в клиенте Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Запишите следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

2. Предоставьте правильное разрешение субъекта-службы в Хранилище BLOB-объектов Azure. Дополнительные сведения о ролях см. в статье [Manage access rights to Azure Storage data with RBAC](../storage/common/storage-auth-aad-rbac.md) (Управление ролями доступа к данным службы хранилища Azure с помощью RBAC).

    - **Для источника** в Системе управления идентификацией и доступом (IAM) предоставьте по крайней мере роль **читателя данных Storage Blob**.
    - В Системе управления идентификацией и доступом (IAM) **приемнику** необходимо выделить роль не ниже **участника для данных больших двоичных объектов хранилища**.

Для связанной службы хранилища BLOB-объектов Azure поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **AzureBlobStorage**. |Да |
| serviceEndpoint | Укажите конечную точку хранилища больших двоичных объектов Azure с шаблоном `https://<accountName>.blob.core.windows.net/`. |Да |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Да |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| tenant | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

>[!NOTE]
>Проверка подлинности субъекта-службы поддерживается только таким типом связанной службы, как AzureBlobStorage, а не предыдущим типом связанной службы AzureStorage.

**Пример**.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Управляемые удостоверения для аутентификации ресурсов Azure

Фабрика данных может быть связана с [управляемым удостоверением для ресурсов Azure](data-factory-service-identity.md), которое представляет эту фабрику данных. Это управляемое удостоверение можно использовать для проверки подлинности хранилища BLOB-объектов, так же как и собственный субъект-службу. Оно разрешает назначенной фабрике данных обращаться к данным и копировать их из службы хранилища BLOB-объектов и в нее.

Общие сведения о проверке подлинности хранилища Azure см. в статье [Authenticate access to Azure Storage using Azure Active Directory](../storage/common/storage-auth-aad.md) (Проверка подлинности при доступе к службе хранилища Azure с помощью Azure Active Directory). Чтобы использовать управляемые удостоверения для проверки подлинности ресурсов Azure, выполните следующие действия.

1. [Получите управляемое удостоверение фабрики данных](data-factory-service-identity.md#retrieve-managed-identity), скопировав значение **идентификатора объекта управляемого удостоверения**, созданного вместе с фабрикой.

2. Предоставьте правильное разрешение управляемому удостоверению в Хранилище BLOB-объектов Azure. Дополнительные сведения о ролях см. в статье [Manage access rights to Azure Storage data with RBAC](../storage/common/storage-auth-aad-rbac.md) (Управление ролями доступа к данным службы хранилища Azure с помощью RBAC).

    - **Для источника** в Системе управления идентификацией и доступом (IAM) предоставьте по крайней мере роль **читателя данных Storage Blob**.
    - В Системе управления идентификацией и доступом (IAM) **приемнику** необходимо выделить роль не ниже **участника для данных больших двоичных объектов хранилища**.

>[!IMPORTANT]
>Если вы используете Polybase для загрузки данных из большого двоичного объекта (используемого в качестве исходного или промежуточного) в хранилище данных SQL, то при использовании проверки подлинности с помощью управляемого удостоверения для большого двоичного объекта обязательно выполните шаги 1 и 2 в [этом руководстве](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) для 1) регистрации сервера базы данных SQL в Azure Active Directory (Azure AD) и 2) назначения роли участника данных BLOB-объекта хранилища серверу базы данных SQL; остальное обрабатывается фабрикой данных. Если хранилище BLOB-объектов настроено с использованием конечной точки виртуальной сети Azure, чтобы использовать Polybase для загрузки данных из нее, требуется проверка подлинности с использованием управляемого удостоверения в соответствии с требованиями Polybase.

Для связанной службы хранилища BLOB-объектов Azure поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **AzureBlobStorage**. |Да |
| serviceEndpoint | Укажите конечную точку хранилища больших двоичных объектов Azure с шаблоном `https://<accountName>.blob.core.windows.net/`. |Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

> [!NOTE]
> Проверка подлинности ресурсов Azure с помощью управляемых удостоверений поддерживается только таким типом связанной службы как AzureBlobStorage, но не предыдущим типом связанной службы AzureStorage. 

**Пример**.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Ниже перечислены свойства, которые поддерживаются для большого двоичного объекта Azure в настройках `location` в наборе данных на основе формата:

| Свойство   | Описание                                                  | Обязательно |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Свойство type для расположения в наборе данных должно иметь значение **AzureBlobStorageLocation**. | Да      |
| контейнер  | Контейнер больших двоичных объектов.                                          | Да      |
| folderPath | Путь к папке в заданном контейнере. Если вы хотите использовать подстановочный знак для фильтрации папки, пропустите этот параметр и укажите его в параметрах источника действия. | нет       |
| fileName   | Имя файла в заданном контейнере + folderPath. Если вы хотите использовать подстановочный знак для фильтрации файлов, пропустите этот параметр и укажите его в параметрах источника действия. | нет       |

**Пример**.

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником и приемником хранилища BLOB-объектов.

### <a name="blob-storage-as-a-source-type"></a>Хранилище BLOB-объектов в качестве типа источника

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Ниже перечислены свойства, которые поддерживаются для большого двоичного объекта Azure в настройках `storeSettings` в источнике копирования на основе формата:

| Свойство                 | Описание                                                  | Обязательно                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Для свойства type в разделе `storeSettings` необходимо задать значение **AzureBlobStorageReadSettings**. | Да                                           |
| ***Поиск файлов для копирования:*** |  |  |
| ВАРИАНТ 1. Статический путь<br> | Копирование из указанного контейнера или папки/пути к файлу, которые указаны в наборе данных. Если вы хотите скопировать все большие двоичные объекты из контейнера/папки, дополнительно укажите `wildcardFileName` как `*`. |  |
| ВАРИАНТ 2. Префикс BLOB-объекта<br>- prefix | Префикс для имени большого двоичного объекта в заданном контейнере, настроенном в наборе данных для фильтрации исходных больших двоичных объектов. Выбираются большие двоичные объекты, имя которых начинается с `container_in_dataset/this_prefix`. Он использует фильтр на стороне службы BLOB-объектов, который более эффективен, чем фильтр с подстановочными знаками. | нет                                                          |
| ВАРИАНТ 3. Подстановочный знак<br>- wildcardFolderPath | Путь к папке с подстановочными знаками в заданном контейнере, настроенном в наборе данных для фильтрации исходных папок. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | нет                                            |
| ВАРИАНТ 3. Подстановочный знак<br>- wildcardFileName | Имя файла с подстановочными знаками в заданном контейнере + folderPath/wildcardFolderPath для фильтрации исходных папок. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ.  Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Да |
| ВАРИАНТ 4. Список файлов<br>- fileListPath | Указывает, что нужно скопировать заданный набор файлов. Укажите текстовый файл, содержащий список файлов, которые необходимо скопировать, указав по одному файлу в строке (относительный путь к пути, заданному в наборе данных).<br/>При использовании этого параметра не указывайте имя файла в наборе данных. Ознакомьтесь с дополнительными примерами в разделе [Примеры списков файлов](#file-list-examples). |нет |
| ***Дополнительные параметры:*** |  | |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике. <br>Допустимые значения: **true** (по умолчанию) и **false**.<br>Это свойство не применяется при настройке `fileListPath`. |нет |
| modifiedDatetimeStart    | Фильтр файлов на основе атрибута: последнее изменение. <br>Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.<br/>Это свойство не применяется при настройке `fileListPath`. | нет                                            |
| modifiedDatetimeEnd      | То же, что и выше.                                               | нет                                            |
| maxConcurrentConnections | Количество одновременных подключений к хранилищу. Укажите, только если требуется ограничить одновременные подключения к хранилищу данных. | нет                                            |

> [!NOTE]
> Для формата Parquet/текста с разделителями источник действия копирования типа **BlobSource**, упомянутый в следующем разделе, по-прежнему поддерживается как есть для обеспечения обратной совместимости. Эту новую модель рекомендуется использовать в дальнейшем. Пользовательский интерфейс создания ADF был изменен для создания этих новых типов.

**Пример**.

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>Хранилище BLOB-объектов в качестве типа приемника

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Ниже перечислены свойства, которые поддерживаются для большого двоичного объекта Azure в настройках `storeSettings` в приемнике копирования на основе формата:

| Свойство                 | Описание                                                  | Обязательно |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Для свойства type в разделе `storeSettings` необходимо задать значение **AzureBlobStorageWriteSettings**. | Да      |
| copyBehavior             | Определяет поведение копирования, когда источником являются файлы из файлового хранилища данных.<br/><br/>Допустимые значения:<br/><b>— PreserveHierarchy (по умолчанию)</b>. Сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><b>— FlattenHierarchy</b>. Все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированные имена. <br/><b>— MergeFiles</b>. Объединяет все файлы из исходной папки в один файл. Если указано имя Blob-объекта или имя файла, то оно присваивается объединенному файлу. В противном случае присваивается автоматически созданное имя файла. | нет       |
| blockSizeInMB | Укажите размер блока в МБ, используемый для записи данных в блочные BLOB-объекты. Подробнее [о блочных BLOB-объектaх](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Допустимое значение — **от 4 до 100 МБ**. <br/>По умолчанию ADF автоматически определяет размер блока на основе типа и данных исходного хранилища. Для недвоичного копирования в большой двоичный объект размер блока по умолчанию составляет 100 МБ, что соответствует объему данных не более 4,95 ТБ. Если объем данных не слишком велик, это может оказаться неоптимальным, особенно при использовании локальной среды выполнения интеграции с плохой сетью, что приводит к истечению времени ожидания или проблемам с производительностью. Можно явно указать размер блока, а также убедиться, что значение параметра blockSizeInMB*50000 достаточно велико для хранения данных, в противном случае выполнение действия копирования завершится ошибкой. | нет |
| maxConcurrentConnections | Количество одновременных подключений к хранилищу. Укажите, только если требуется ограничить одновременные подключения к хранилищу данных. | нет       |

**Пример**.

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Примеры фильтров папок и файлов

В этом разделе описываются результаты применения фильтров с подстановочными знаками к пути папки и имени файла.

| folderPath | fileName | recursive | Структура исходной папки и результат фильтрации (извлекаются файлы, выделенные **полужирным** шрифтом)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (пусто, используйте по умолчанию) | false | контейнер<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `container/Folder*` | (пусто, используйте по умолчанию) | Да | контейнер<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `container/Folder*` | `*.csv` | false | контейнер<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `container/Folder*` | `*.csv` | Да | контейнер<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |

### <a name="file-list-examples"></a>Примеры списков файлов

В этом разделе описывается результирующее поведение при использовании пути к списку файлов в источнике действия копирования.

Предположим, что у вас есть следующая исходная структура папок и вы хотите скопировать файлы, выделенные полужирным шрифтом:

| Пример исходной структуры                                      | Содержимое файла FileListToCopy.txt                             | Конфигурация ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| контейнер<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Метаданные<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | Файл1.csv<br>Вложенная_папка1/Файл3.csv<br>Вложенная_папка1/Файл5.csv | **В наборе данных:**<br>- Контейнер: `container`<br>- Путь к папке: `FolderA`<br><br>**В источнике действия копирования:**<br>- Путь к списку файлов: `container/Metadata/FileListToCopy.txt` <br><br>Путь к списку файлов указывает на текстовый файл в том же хранилище данных, содержащий список файлов, которые необходимо скопировать, указав по одному файлу в строке с относительным путем к пути, заданному в наборе данных. |

### <a name="some-recursive-and-copybehavior-examples"></a>Некоторые примеры recursive и copyBehavior

В данном разделе описываются результаты выполнения операции копирования при использовании различных сочетаний значений recursive и copyBehavior.

| recursive | copyBehavior | Структура папок источника | Результаты цели |
|:--- |:--- |:--- |:--- |
| Да |preserveHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается с такой же структурой, как и исходная папка:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 |
| Да |flattenHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл2"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл3"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл4"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл5" |
| Да |mergeFiles | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1", "Файл2", "Файл3", "Файл4" и "Файл5" объединяется в один файл с автоматически созданным именем. |
| false |preserveHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой. <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| false |flattenHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой. <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл2"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| false |mergeFiles | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1" и "Файл2" объединяется в один файл с автоматически созданным именем. автоматически созданное имя для "Файл1"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |

## <a name="preserve-metadata-during-copy"></a>Сохранение метаданных файла во время копирования

При копировании файлов из Amazon S3/BLOB-объекта Azure или Azure Data Lake Storage 2-го поколения в Azure Data Lake Storage 2-го поколения или BLOB-объект Azure можно сохранить метаданные файла вместе с данными. Подробнее см. в разделе [Сохранение метаданных](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Свойства потока данных для сопоставления

При преобразовании данных в потоке данных для сопоставления можно считывать и записывать файлы из хранилища BLOB-объектов Azure в формате JSON, Avro, в формате текста с разделителями или в формате Parquet. Дополнительные сведения см. в описаниях [преобразования источника](data-flow-source.md) и [преобразования приемника](data-flow-sink.md) в разделе, посвященном функции потока данных для сопоставления.

### <a name="source-transformation"></a>Преобразование источника

В преобразовании источника можно выполнять чтение из контейнера, папки или отдельного файла в хранилище BLOB-объектов Azure. Настройки управления чтением файлов находятся на вкладке **Параметры источника**. 

![Параметры источника](media/data-flow/sourceOptions1.png "Параметры источника")

**Путь с символами подстановки:** Использование шаблона с подстановочными знаками позволяет указать потоку ADF, что необходимо перебрать каждую соответствующую папку и файл в одном преобразовании источника. Это эффективный способ обработки нескольких файлов в одном потоке. Добавьте несколько шаблонов сопоставления с подстановочными знаками с помощью значка плюса ("+"), который появляется при наведении указателя мыши на существующий шаблон с подстановочными знаками.

В исходном контейнере выберите файлы, соответствующие шаблону. В наборе данных можно указать только контейнер. Путь с подстановочными знаками должен также включать путь к папке из корневой папки.

Примеры подстановочных знаков:

* ```*``` — любой набор символов
* ```**``` — рекурсивная вложенность каталога
* ```?``` — заменяет один символ
* ```[]``` — соответствие по одному или нескольким символам в квадратных скобках

* ```/data/sales/**/*.csv``` — возвращает все файлы CSV в папке /data/sales
* ```/data/sales/20??/**/``` — возвращает все файлы, созданные в 20 веке
* ```/data/sales/*/*/*.csv``` — возвращает файлы CSV, расположенные двумя уровнями ниже в папке /data/sales
* ```/data/sales/2004/*/12/[XY]1?.csv``` — возвращает все файлы CSV, созданные в декабре 2004 г., которые начинаются с X или Y с двузначным числом в качестве префикса

**Корневая папка раздела:** Если в источнике файлов имеются секционированные папки формата ```key=value``` (например, year=2019), то верхний уровень этого дерева секционированной папки можно назначить имени столбца в потоке данных.

Во-первых, задайте подстановочный знак, чтобы включить все пути, которые являются в секционированных папках, а также конечные файлы, которые вы хотите прочитать.

![Параметры исходного файла раздела](media/data-flow/partfile2.png "Параметр файла раздела")

Для определения верхнего уровня структуры папок используйте параметр "Корневая папка раздела". При просмотре содержимого данных с помощью предварительного просмотра данных вы увидите, что ADF добавит разрешенные разделы, найденные на каждом уровне папок.

![Корневая папка раздела](media/data-flow/partfile1.png "Предварительный просмотр корневой папки раздела")

**Список файлов:** Это набор файлов. Создайте текстовый файл, который включает список относительных путей файлов для обработки. Укажите на этот текстовый файл.

**Столбец для хранения имени файла:** Сохраните имя исходного файла в столбце данных. Укажите здесь новое имя столбца для хранения строки имени файла.

**После завершения:** Укажите, что делать после завершения: ничего не делать с исходным файлом после выполнения потока данных, удалить или переместить исходные файлы. Пути для перемещения являются относительными.

Чтобы переместить исходные файлы в другое расположение после обработки, сначала выберите "Переместить" для операции с файлом. Затем задайте исходный каталог. Если вы не используете подстановочные знаки в пути, исходным каталогом будет та же папка, что и исходная папка.

Если у вас есть исходный путь с подстановочным знаком, синтаксис будет выглядеть следующим образом:

```/data/sales/20??/**/*.csv```

В качестве исходной папки можно указать

```/data/sales```

В качестве целевой папки можно указать

```/backup/priorSales```

В этом случае все файлы, источником которых является папка /data/sales, перемещаются в папку /backup/priorSales.

> [!NOTE]
> Операции с файлами выполняются только в том случае, если поток данных выполняется из запуска конвейера (отладка конвейера или запуск выполнения) с использованием действия выполнения потока данных в конвейере. Операции с файлами *не* выполняются в режиме отладки потока данных.

**Фильтровать по дате последнего изменения:** Вы можете отфильтровать обрабатываемые файлы, указав диапазон дат последнего изменения. Все значения даты и времени указаны в формате UTC. 

### <a name="sink-properties"></a>Свойства приемника

В преобразовании приемника можно выполнять запись в контейнер или папку в хранилище BLOB-объектов Azure. Настройки управления записью файлов находятся на вкладке **Параметры**.

![Параметры приемника](media/data-flow/file-sink-settings.png "Параметры приемника")

**Очистить папку:** определяет, следует ли удалять конечную папку перед записью данных.

**Параметр имени файла:** определяет способ присвоения имени целевым файлам в папке назначения. Доступные параметры имени файла:
   * **По умолчанию**: разрешить Spark присваивать имена файлам на основе значений по умолчанию PART.
   * **Шаблон**: введите шаблон, в котором перечислены выходные файлы для каждого раздела. Например, при использовании шаблона **loans[n].csv** будут создаваться файлы loans1.csv, loans2.csv и т. д.
   * **На один раздел**: введите имя файла для одного раздела.
   * **Как данные в столбце**: задайте для выходного файла значение столбца. Путь задается относительно контейнера набора данных, а не папки назначения. Если в наборе данных имеется путь к папке, он будет переопределен.
   * **Вывод в один файл**: объединение секционированных выходных файлов в один именованный файл. Путь задается относительно папки набора данных. Имейте в виду, что операция слияния может завершиться сбоем в зависимости от размера узла. Этот параметр не рекомендуется использовать для больших наборов данных.

**Все в кавычках:** указывает, следует ли заключать все значения в кавычки

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Подробные сведения об этих свойствах см. в разделе [Действие поиска](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Свойства действия GetMetadata

Подробные сведения об этих свойствах см. в разделе [Действие GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Свойства действия удаления

Подробные сведения об этих свойствах см. в разделе [Действие удаления](delete-activity.md)

## <a name="legacy-models"></a>Устаревшие модели

>[!NOTE]
>Следующие модели по-прежнему поддерживаются на условиях "как есть" для обеспечения обратной совместимости. Новую модель, упомянутую в разделах выше, рекомендуется использовать в дальнейшем. Пользовательский интерфейс создания ADF был изменен для создания новой модели.

### <a name="legacy-dataset-model"></a>Устаревшая модель набора данных

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **AzureBlob**. |Да |
| folderPath | Путь контейнеру и папке в хранилище BLOB-объектов. <br/><br/>Фильтр с подстановочными знаками поддерживается для пути без имени контейнера. Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br/><br/>Например: myblobcontainer/myblobfolder//. Дополнительные примере см. в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). |Yes для действия Copy/Lookup, No для действия GetMetadata |
| fileName | **Имя или фильтр постановочных знаков** для файлов по указанному folderPath. Если этому свойству не присвоить значение, набор данных будет указывать на все большие двоичные объекты в папке. <br/><br/>Допустимые знаки подстановки для фильтра: `*` (соответствует нулю или нескольким символам) и `?` (соответствует нулю или одному символу).<br/>Пример 1. `"fileName": "*.csv"`<br/>Пример 2. `"fileName": "???20180427.txt"`<br/>Используйте `^` для экранирования символов, если фактическое имя файла содержит подстановочный знак или этот escape-символ.<br/><br/>Если свойство fileName для выходного набора данных не указано, а **preserveHierarchy** не указано в приемнике действия, действие копирования автоматически создаст имя большого двоичного объекта в следующем формате: "*Data.[GUID идентификатора выполнения действия].[GUID, если поведение — FlattenHierarchy].[формат, если настроено].[сжатие, если настроено]* ". Например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz. При копировании из табличного источника с использованием имени таблицы вместо запроса формат имени будет таким: " *[имя таблицы].[фoрмат].[сжатие, если настроено]* ". Например, "MyTable.csv". |нет |
| modifiedDatetimeStart | Фильтр файлов на основе атрибута: последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Учтите, что включение этого параметра в случае, если требуется использовать фильтр файлов из огромных объемов файлов, повлияет на общую производительность перемещения данных. <br/><br/> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| нет |
| modifiedDatetimeEnd | Фильтр файлов на основе атрибута: последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Учтите, что включение этого параметра в случае, если требуется использовать фильтр файлов из огромных объемов файлов, повлияет на общую производительность перемещения данных. <br/><br/> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| нет |
| format | Если требуется скопировать файлы между файловыми хранилищами "как есть" (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Если необходимо проанализировать или создать файлы определенного формата, поддерживаются следующие форматы файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** и **ParquetFormat**. Свойству **type** в разделе **format** необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs-legacy.md#text-format), [формате JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format) и [формате Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Optimal** и **Fastest**. |нет |

>[!TIP]
>Чтобы скопировать все большие двоичные объекты в папке, укажите только **folderPath**.<br>Чтобы скопировать один большой двоичный объект с заданным именем, укажите **folderPath** с частью папки и **fileName** с именем файла.<br>Чтобы скопировать подмножество больших двоичных объектов в папке, укажите **folderPath** с частью папки и **fileName**, используя фильтр подстановочных знаков. 

**Пример**.

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Устаревшая модель источника действия копирования

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **BlobSource**. |Да |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике.<br/>Допустимые значения: **true** (по умолчанию) и **false**. | нет |
| maxConcurrentConnections | Количество одновременных подключений к хранилищу. Укажите, только если требуется ограничить одновременные подключения к хранилищу данных. | нет |

**Пример**.

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Устаревшая модель приемника действия копирования

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа приемника действия копирования должно иметь значение **BlobSink**. |Да |
| copyBehavior | Определяет поведение копирования, когда источником являются файлы из файлового хранилища данных.<br/><br/>Допустимые значения:<br/><b>— PreserveHierarchy (по умолчанию)</b>. Сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><b>— FlattenHierarchy</b>. Все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированные имена. <br/><b>— MergeFiles</b>. Объединяет все файлы из исходной папки в один файл. Если указано имя Blob-объекта или имя файла, то оно присваивается объединенному файлу. В противном случае присваивается автоматически созданное имя файла. | нет |
| maxConcurrentConnections | Количество одновременных подключений к хранилищу. Укажите, только если требуется ограничить одновременные подключения к хранилищу данных. | нет |

**Пример**.

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия

В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
