---
title: Копирование и преобразование данных в хранилище Azure Blob
description: Узнайте, как копировать данные в хранилище Blob и из нее, а также преобразовывать данные в хранилище Blob с помощью Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: dd13a08b3c2f63baf509efbb730032edd4eba61a
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011554"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Копирование и преобразование данных в хранилище Azure Blob с помощью Azure Data Factory

> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-azure-blob-connector.md)
> * [Текущая версия](connector-azure-blob-storage.md)

В этой статье описывается, как использовать активность копирования в фабрике данных Azure для копирования данных из и в хранилище Azure Blob, а также использовать поток данных для преобразования данных в хранилище Azure Blob. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

>[!TIP]
>Подробнее о сценарии миграции хранилища данных или хранилища данных можно узнать больше из [Группы данных «Использование Лазурного» для переноса данных из озера данных или хранилища данных в Azure.](data-migration-guidance-overview.md)

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем Azure Blob поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Картирование потока данных](concepts-data-flow-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
- [Удаление действия](delete-activity.md)

Для деятельности Copy этот разъем для хранения Blob поддерживает:

- Копирование больших двоичных объектов в учетные записи хранения Azure общего назначения, в хранилище горячих и холодных BLOB-объектов и из них. 
- Копирование BLOB-объектов с помощью ключа учетной записи, проверка подлинности ресурсов Azure на основе подписанного URL-адреса, субъект-службы или управляемых удостоверений.
- Копирование больших двоичных объектов из блочных, добавочных или страничных BLOB-объектов, а также копирование данных только в блочные BLOB-объекты.
- Копирование больших двоичных объектов "как есть", анализ или создание больших двоичных объектов путем использования [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).
- [Сохранить метаданные файлов во время копирования](#preserve-metadata-during-copy).

>[!IMPORTANT]
>Если вы позволяете **доверенным службам Microsoft Allow** доверять для доступа к этой опции учетной записи хранилища в настройках брандмауэра Azure Storage и хотите использовать время выполнения интеграции Azure для подключения к хранилищу Blob, необходимо использовать управляемую [проверку подлинности идентификации.](#managed-identity)

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для хранилища BLOB-объектов.

## <a name="linked-service-properties"></a>Свойства связанной службы

Дополнительные сведения о типах аутентификации, поддерживаемых соединителем больших двоичных объектов Azure, см. в следующих разделах.

- [Проверка подлинности на основе ключа учетной записи](#account-key-authentication)
- [Аутентификация SAS](#shared-access-signature-authentication)
- [Главная аутентификация службы](#service-principal-authentication)
- [Управляемые удостоверения для проверки подлинности ресурсов Azure](#managed-identity)

>[!NOTE]
>При использовании PolyBase для загрузки данных в хранилище данных S'L, если ваш источник или постановка хранилища Blob настроена с помощью конечной точки виртуальной сети, необходимо использовать управляемую аутентификацию идентификации, как того требует PolyBase, и использовать самоходной интеграции Runtime с версией 3.18 или выше. Просмотрите [раздел управляемой проверки подлинности с](#managed-identity) большим количеством предпосылок конфигурации.

>[!NOTE]
>Мероприятия HDInsights и Azure Machine Learning поддерживают только проверку ключевой учетной информации учетной записи хранилища Azure Blob.

### <a name="account-key-authentication"></a>Проверка подлинности на основе ключа учетной записи

При использовании проверки подлинности на основе ключа учетной записи поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойству type должно быть присвоено значение **AzureBlobStorage** (рекомендуется) или **AzureStorage** (см. примечания ниже). |Да |
| connectionString | В свойстве connectionString указываются сведения, необходимые для подключения к службе хранилища. <br/> Вы можете также поместить ключ учетной записи в Azure Key Vault и извлечь конфигурацию `accountKey` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| connectVia | [Время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

>[!NOTE]
>Второстепенная конечная точка службы Blob не поддерживается при использовании аутентификации ключа учетной записи. Вы можете использовать другие типы аутентификации.

>[!NOTE]
>При использовании связанной службы тип AzureStorage по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый тип связанной службы — AzureBlobStorage.

**Примере:**

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

Подпись общего доступа обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения. Вы можете использовать подписанный URL-адрес, чтобы предоставить клиенту ограниченные разрешения на работу с объектами в вашей учетной записи хранения на определенный период. Не нужно предоставлять совместный доступ к ключам доступа для учетной записи. Подписанный URL-адрес — это универсальный код ресурса (URI), который в своих параметрах запроса содержит все сведения, необходимые для доступа к ресурсу хранилища с прохождением аутентификации. Для доступа к ресурсам хранилища с помощью подписанного URL-адреса клиенту достаточно передать SAS в соответствующий конструктор или метод. Дополнительные сведения о подписанном URL-адресе см. в статье [Использование подписанных URL-адресов (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Сейчас Фабрика данных поддерживает как **подписанные URL-адреса уровня службы**, так и **подписанные URL-адреса уровня учетной записи**. Для получения дополнительной информации об подписях общего доступа см. [Грант ограниченный доступ к ресурсам хранения Azure с использованием общих подписей доступа (SAS).](../storage/common/storage-sas-overview.md)
>- В более поздней конфигурации набора данных путь к папке — это полный путь, начинающийся с уровня контейнера. Вам нужно настроить такой путь, соответствующий пути в вашем URI SAS.

При использовании аутентификации SAS поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойству type должно быть присвоено значение **AzureBlobStorage** (рекомендуется) или **AzureStorage** (см. примечания ниже). |Да |
| sasUri | Укажите URI подписанного URL-адреса для ресурсов службы хранилища, например для большого двоичного объекта или контейнера. <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы также можете поместить токен SAS в Azure Key Vault, чтобы использовать автоматическое вращение и удалить часть токенов. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| connectVia | [Время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

>[!NOTE]
>При использовании связанной службы тип AzureStorage по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый тип связанной службы — AzureBlobStorage.

**Примере:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
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
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
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
| connectVia | [Время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

>[!NOTE]
>Проверка подлинности субъекта-службы поддерживается только таким типом связанной службы, как AzureBlobStorage, а не предыдущим типом связанной службы AzureStorage.

**Примере:**

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Управляемые идентификаторы для проверки подлинности ресурсов Azure

Фабрика данных может быть связана с [управляемым удостоверением для ресурсов Azure](data-factory-service-identity.md), которое представляет эту фабрику данных. Вы можете непосредственно использовать эту управляемую идентификацию для проверки подлинности хранения данных Blob, аналогичной использованию собственного принципала обслуживания. Оно разрешает назначенной фабрике данных обращаться к данным и копировать их из службы хранилища BLOB-объектов и в нее.

Общие сведения о проверке подлинности хранилища Azure см. в статье [Authenticate access to Azure Storage using Azure Active Directory](../storage/common/storage-auth-aad.md) (Проверка подлинности при доступе к службе хранилища Azure с помощью Azure Active Directory). Чтобы использовать управляемые удостоверения для проверки подлинности ресурсов Azure, выполните следующие действия.

1. [Retrieve data factory managed identity information](data-factory-service-identity.md#retrieve-managed-identity) by copying the value of managed identity object **ID** generated generated along with your factory.

2. Предоставьте правильное разрешение управляемому удостоверению в Хранилище BLOB-объектов Azure. Дополнительные сведения о ролях см. в статье [Manage access rights to Azure Storage data with RBAC](../storage/common/storage-auth-aad-rbac.md) (Управление ролями доступа к данным службы хранилища Azure с помощью RBAC).

    - **Для источника** в Системе управления идентификацией и доступом (IAM) предоставьте по крайней мере роль **читателя данных Storage Blob**.
    - В Системе управления идентификацией и доступом (IAM) **приемнику** необходимо выделить роль не ниже **участника для данных больших двоичных объектов хранилища**.

>[!IMPORTANT]
>Если вы используете PolyBase для загрузки данных из Blob (в качестве источника или как постановки) в хранилище данных S'L, при использовании управляемой проверки подлинности для Blob, убедитесь, что вы также следуете шагам 1 и 2 в [этом руководстве](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 1) зарегистрируйте свой сервер базы данных S'L с Azure Active Directory (Azure AD) и 2) присваивайте роль вкладчика базы данных Storage Blob. остальные обрабатываются Data Factory. Если хранилище Blob настроено с помощью конечной точки виртуальной сети Azure, чтобы использовать PolyBase для загрузки данных из него, необходимо использовать управляемую аутентификацию идентификации, как того требует PolyBase.

Для связанной службы хранилища BLOB-объектов Azure поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **AzureBlobStorage**. |Да |
| serviceEndpoint | Укажите конечную точку хранилища больших двоичных объектов Azure с шаблоном `https://<accountName>.blob.core.windows.net/`. |Да |
| connectVia | [Время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

> [!NOTE]
> Проверка подлинности ресурсов Azure с помощью управляемых удостоверений поддерживается только таким типом связанной службы как AzureBlobStorage, но не предыдущим типом связанной службы AzureStorage. 

**Примере:**

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

Следующие свойства поддерживаются для Azure Blob в `location` настройках в наборе данных на основе формата:

| Свойство   | Описание                                                  | Обязательно |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Свойство типа местоположения в наборе данных должно быть установлено в **AzureBlobStorageLocation.** | Да      |
| контейнер  | Контейнер с каплями.                                          | Да      |
| folderPath | Путь к папке под данным контейнером. Если требуется использовать подстановочный знак для фильтрации папки, пропустите эту настройку и укажите в настройках источника активности. | нет       |
| fileName   | Имя файла под данном контейнером и папкойPath. Если вы хотите использовать подстановочный знак для фильтрации файлов, пропустите эту настройку и укажите в настройках источника активности. | нет       |

**Примере:**

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

Следующие свойства поддерживаются для Azure Blob под `storeSettings` настройками в источнике копирования на основе формата:

| Свойство                 | Описание                                                  | Обязательно                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Свойство типа `storeSettings` под должны быть установлены на **AzureBlobStorageReadSettings.** | Да                                           |
| recursive                | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике. Разрешенные значения **верны** (по умолчанию) и **ложные.** | нет                                            |
| prefix                   | Префикс для имени капли под данным контейнером, настроенным в наборе данных для фильтрации капли источника. Выбираются blobs, имя которых начинается с этой префикса. <br>Применяется только `wildcardFolderPath` `wildcardFileName` тогда, когда и свойства не указаны. | нет                                                          |
| подстановочный знакFolderPath       | Путь папки с символами подстановочных знаков под данным контейнером, настроенным в наборе данных для фильтрации папок источника. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | нет                                            |
| подстановочный знакFileName         | Имя файла с символами подстановочных знаков под данным контейнером и папкойPath/wildcardFolderPath для фильтрации исходных файлов. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ.  Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Да, `fileName` если не указан в наборе данных |
| modifiedDatetimeStart    | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени. | нет                                            |
| modifiedDatetimeEnd      | То же, что и выше.                                               | нет                                            |
| maxConcurrentConnections | Одновременное число подключений для подключения к хранилищу. Указать только при ограничении одновременного подключения к хранилику данных. | нет                                            |

> [!NOTE]
> Для формата текста Parquet/delimited источник активности типа **BlobSource,** упомянутый в следующем разделе, по-прежнему поддерживается как для обратной совместимости. Вам предлагается использовать эту новую модель в будущем, и aDF авторство UI переключился на генерацию этих новых типов.

**Примере:**

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

Следующие свойства поддерживаются для Azure Blob под `storeSettings` настройками в формате на основе копий раковины:

| Свойство                 | Описание                                                  | Обязательно |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Свойство типа `storeSettings` под должны быть установлены на **AzureBlobStorageWriteSettings.** | Да      |
| copyBehavior             | Определяет поведение копирования, когда источником являются файлы из файлового хранилища данных.<br/><br/>Допустимые значения:<br/><b>— PreserveHierarchy (по умолчанию)</b>. Сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><b>— FlattenHierarchy</b>. Все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированные имена. <br/><b>— MergeFiles</b>. Объединяет все файлы из исходной папки в один файл. Если указано имя Blob-объекта или имя файла, то оно присваивается объединенному файлу. В противном случае присваивается автоматически созданное имя файла. | нет       |
| blockSizeinMB | Укажите размер блока в МБ, используемый для записи данных для блокировки капли. Узнайте больше [о Block Blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Разрешенное значение составляет **от 4 до 100 МБ.** <br/>По умолчанию ADF автоматически определяет размер блока на основе типа и данных исходного хранилища. Для небирной копии в Blob размер блока по умолчанию составляет 100 МБ, чтобы вписаться не более чем в 4,95 ТБ.. Это может быть не оптимальным, когда ваши данные не являются большими, особенно когда вы используете самостоятельно размещенную интеграцию Runtime с плохой сетью, что приводит к тайм-ауту работы или проблеме с производительностью. Можно явно указать размер блока, при этом обеспечить, чтобы блокSizeInMB-50000 был достаточно большим для хранения данных, в противном случае запуск активности копирования не удастся. | нет |
| maxConcurrentConnections | Одновременное число подключений для подключения к хранилищу. Указать только при ограничении одновременного подключения к хранилику данных. | нет       |

**Примере:**

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

| folderPath | fileName | recursive | Структура папки исхода и результат фильтра (файлы **жирным шрифтом** извлекаются)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (пусто, используйте по умолчанию) | false | контейнер<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `container/Folder*` | (пусто, используйте по умолчанию) | true | контейнер<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `container/Folder*` | `*.csv` | false | контейнер<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `container/Folder*` | `*.csv` | true | контейнер<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Некоторые примеры recursive и copyBehavior

В данном разделе описываются результаты выполнения операции копирования при использовании различных сочетаний значений recursive и copyBehavior.

| recursive | copyBehavior | Структура папок источника | Результаты цели |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается с такой же структурой, как и исходная папка:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 |
| true |flattenHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл2"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл3"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл4"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл5" |
| true |mergeFiles | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1", "Файл2", "Файл3", "Файл4" и "Файл5" объединяется в один файл с автоматически созданным именем. |
| false |preserveHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой. <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| false |flattenHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой. <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл2"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| false |mergeFiles | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1" и "Файл2" объединяется в один файл с автоматически созданным именем. автоматически созданное имя для "Файл1"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |

## <a name="preserve-metadata-during-copy"></a>Сохранение метаданных во время копирования

При копировании файлов из Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 в Azure Data Lake Storage Gen2/Azure Blob можно сохранить метаданные файлов вместе с данными. Узнайте больше из [метаданных заповедника](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Отображение свойств потока данных

При преобразовании данных в картографируем поток данных можно читать и писать файлы из azure Blob Storage в формате JSON, Avro, Delimited Text или Parquet. Для получения дополнительной информации см. [преобразование источника](data-flow-source.md) и преобразование [раковины](data-flow-sink.md) в функции потока данных отображения.

### <a name="source-transformation"></a>Преобразование источника

В исходной трансформации можно прочитать из контейнера, папки или отдельного файла в Хранилище Azure Blob. Вкладка **параметры «Источник»** позволяет управлять чтением файлов. 

![Варианты исхода](media/data-flow/sourceOptions1.png "Варианты исхода")

**Путь Wildcard:** Использование шаблона подстановочных знаков поручает ADF прослежять каждую соответствующую папку и файл в одном преобразовании Исходного кода. Это эффективный способ обработки нескольких файлов в одном потоке. Добавьте несколько шаблонов подстановочного знака, который появляется при нависшем над существующим шаблоном подстановочных знаков.

Из исходного контейнера выберите серию файлов, которые соответствуют шаблону. Только контейнер может быть указан в наборе данных. Таким образом, ваш путь подстановочного знака должен также включать путь папки из корневой папки.

Примеры Wildcard:

* ```*```Представляет любой набор символов
* ```**```Представляет собой рекурсивное вложение каталога
* ```?```Заменяет одного символа
* ```[]```Соответствует одному из символов в скобках

* ```/data/sales/**/*.csv```Получает все файлы csv под /данными/продажами
* ```/data/sales/20??/**/```Получает все файлы в 20-м веке
* ```/data/sales/*/*/*.csv```Получает csv файлы двух уровней под /данные / продажи
* ```/data/sales/2004/*/12/[XY]1?.csv```Получает все файлы csv в 2004 году в декабре, начиная с X или Y префиксированы двузначным числом

**Раздел Корневой путь:** Если в исходном исходном материале с ```key=value``` форматом (например, год-2019) есть раздельные папки с форматом (например, год 2019), то можно назначить верхний уровень этого дерева папки раздела к имени столбца в потоке данных.

Во-первых, установите подстановочный знак, чтобы включить все пути, которые разделены папки плюс лист файлы, которые вы хотите прочитать.

![Настройки исходного кода раздела](media/data-flow/partfile2.png "Настройка файла раздела")

Используйте настройки раздела Root Path, чтобы определить, что такое верхний уровень структуры папки. При просмотре содержимого данных с помощью предварительного просмотра данных можно увидеть, что ADF добавит разрешенные разделы, найденные в каждом из уровней папки.

![Путь корня раздела](media/data-flow/partfile1.png "Предварительный просмотр корневого пути раздела")

**Список файлов:** Это набор файлов. Создайте текстовый файл, включающий список относительного пути обработки файлов. Укажите на этот текстовый файл.

**Колонка для хранения имени файла:** Храните имя исходного файла в столбце в данных. Введите новое имя столбца здесь для хранения строки имени файла.

**После завершения:** Выберите ничего не делать с исходным файлом после запуска потока данных, удаления исходного файла или перемещения исходного файла. Пути для перемещения являются относительными.

Чтобы переместить исходные файлы в другое место после обработки, сначала выберите "Движение" для операции файлов. Затем установите "из" каталога. Если вы не используете какие-либо подстановочные знаки для вашего пути, то параметр "от" будет той же папкой, что и папка исходного кода.

Если у вас есть исходный путь с подстановочным знаком, ваш синтаксис будет выглядеть следующим образом:

```/data/sales/20??/**/*.csv```

Вы можете указать "от" как

```/data/sales```

И "к", как

```/backup/priorSales```

В этом случае все файлы, полученные в соответствии с /данными/продажами, перемещаются в /backup/priorSales.

> [!NOTE]
> Операции файлов выполняются только при запуске потока данных из запуска конвейера (отладка или выполнение конвейера), используюго действие потока данных в конвейере. Операции файлов *не* работают в режиме отладки потока данных.

**Фильтр по последним модифицированным:** Можно фильтровать файлы, которые вы обрабатываете, указывая диапазон дат, когда они были в последний раз изменены. Все даты-время в UTC. 

### <a name="sink-properties"></a>Свойства раковины

При преобразовании раковины можно написать в контейнер или папку в Хранилище Azure Blob. вкладка **«Настройки»** позволяет управлять написанием файлов.

![варианты раковины](media/data-flow/file-sink-settings.png "варианты раковины")

**Очистить папку:** Определяет, очищается ли папка назначения до их написания данных.

**Опция названия файла:** Определяет, как будут названы файлы назначения в папке назначения. Параметры имени файла:
   * **По умолчанию**: Разрешить Spark называть файлы на основе по умолчанию PART.
   * **Шаблон**: Введите шаблон, перечисляет выводные файлы на раздел. Например, **кредиты будут** создавать кредиты1.csv, loans2.csv и так далее.
   * **Для раздела**: Введите одно имя файла на раздел.
   * **Как данные в столбце:** Установите выходный файл на значение столбца. Путь относительно контейнера набора данных, а не папки назначения. Если в наборе данных есть путь папки, он будет перекрыт.
   * **Выход в один файл:** Объедините разделенные выходные файлы в один названный файл. Путь относительно папки набора данных. Пожалуйста, имейте в виду, что операция te merge может быть сбой в зависимости от размера узла. Этот параметр не рекомендуется для больших наборов данных.

**Цитата все:** Определяет, следует ли приложить все значения в кавычки

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Свойства активности GetMetadata

Чтобы узнать подробности о свойствах, проверьте [активность GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Удаление свойств активности

Чтобы узнать подробности о свойствах, проверьте [действия Delete](delete-activity.md)

## <a name="legacy-models"></a>Устаревшие модели

>[!NOTE]
>Следующие модели по-прежнему поддерживаются как для обратной совместимости. Вы предложили использовать новую модель, упомянутую в вышеуказанных разделах в будущем, и ADF авторство UI переключился на генерацию новой модели.

### <a name="legacy-dataset-model"></a>Устаревшая модель набора данных

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено **в AzureBlob.** |Да |
| folderPath | Путь контейнеру и папке в хранилище BLOB-объектов. <br/><br/>Фильтр с подстановочными знаками поддерживается для пути без имени контейнера. Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br/><br/>Например: myblobcontainer/myblobfolder//. Дополнительные примере см. в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). |Yes для действия Copy/Lookup, No для действия GetMetadata |
| fileName | **Имя или фильтр постановочных знаков** для файлов по указанному folderPath. Если этому свойству не присвоить значение, набор данных будет указывать на все большие двоичные объекты в папке. <br/><br/>Допустимые знаки подстановки для фильтра: `*` (соответствует нулю или нескольким символам) и `?` (соответствует нулю или одному символу).<br/>Пример 1. `"fileName": "*.csv"`<br/>Пример 2. `"fileName": "???20180427.txt"`<br/>Используйте `^` для экранирования символов, если фактическое имя файла содержит подстановочный знак или этот escape-символ.<br/><br/>Когда fileName не указан для набора данных вывода и **сохраненияИерархия** не указана в поглотителе активности, активность копирования автоматически генерирует имя капли со следующим шаблоном:*«Данные». действия запустить ID GUID. (ГУИД, если ФлэттенИерархии). «формат, если настроен». «Сжатие при настройке»,* например, «Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz»; если вы копируете из табликового источника, используя название таблицы вместо запроса, шаблон имени —*«имя стола». формата. «Сжатие при настройке»,* например, «MyTable.csv». |нет |
| modifiedDatetimeStart | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Имейте в виду, общая производительность движения данных будет влияние, позволяя эту настройку, когда вы хотите сделать файл фильтр из огромного количества файлов. <br/><br/> Свойства могут быть NULL, что означает, что фильтр атрибутов файла не будет применен к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| нет |
| modifiedDatetimeEnd | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Имейте в виду, общая производительность движения данных будет влияние, позволяя эту настройку, когда вы хотите сделать файл фильтр из огромного количества файлов. <br/><br/> Свойства могут быть NULL, что означает, что фильтр атрибутов файла не будет применен к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| нет |
| format | Если требуется скопировать файлы между файловыми хранилищами "как есть" (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Если нужно проанализировать или создать файлы определенного формата, поддерживаются следующие типы форматов файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** и **ParquetFormat**. Свойству **type** в разделе **format** необходимо присвоить одно из этих значений. Для получения дополнительной информации, см [Текст формата](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Формат JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [формат Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [orc формат](supported-file-formats-and-compression-codecs-legacy.md#orc-format), и формат [паркета](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) разделов. |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Optimal** и **Fastest**. |нет |

>[!TIP]
>Чтобы скопировать все большие двоичные объекты в папке, укажите только **folderPath**.<br>Чтобы скопировать один большой двоичный объект с заданным именем, укажите **folderPath** с частью папки и **fileName** с именем файла.<br>Чтобы скопировать подмножество больших двоичных объектов в папке, укажите **folderPath** с частью папки и **fileName**, используя фильтр подстановочных знаков. 

**Примере:**

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

### <a name="legacy-copy-activity-source-model"></a>Модель источника исходной деятельности устаревшей копирования

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования должно быть установлено **на BlobSource.** |Да |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике.<br/>Разрешенные значения **верны** (по умолчанию) и **ложные.** | нет |
| maxConcurrentConnections | Одновременное число подключений для подключения к хранилищу. Указать только при ограничении одновременного подключения к хранилику данных. | нет |

**Примере:**

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

### <a name="legacy-copy-activity-sink-model"></a>Устаревшая модель погружения активности копирования

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа раковины активности копирования должно быть установлено на **BlobSink.** |Да |
| copyBehavior | Определяет поведение копирования, когда источником являются файлы из файлового хранилища данных.<br/><br/>Допустимые значения:<br/><b>— PreserveHierarchy (по умолчанию)</b>. Сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><b>— FlattenHierarchy</b>. Все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированные имена. <br/><b>— MergeFiles</b>. Объединяет все файлы из исходной папки в один файл. Если указано имя Blob-объекта или имя файла, то оно присваивается объединенному файлу. В противном случае присваивается автоматически созданное имя файла. | нет |
| maxConcurrentConnections | Одновременное число подключений для подключения к хранилищу. Указать только при ограничении одновременного подключения к хранилику данных. | нет |

**Примере:**

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

## <a name="next-steps"></a>Следующие шаги

В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
