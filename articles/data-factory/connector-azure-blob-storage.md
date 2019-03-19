---
title: Копирование данных в хранилище BLOB-объектов Azure и из него с помощью фабрики данных | Документация Майкрософт
description: Узнайте, как копировать данные из исходных хранилищ данных в хранилище BLOB-объектов Azure или из него в поддерживаемый приемник хранилища данных с помощью фабрики данных.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: jingwang
ms.openlocfilehash: 115a02c7f8abee18c226c127fb84b4bb34250cd0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456318"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Копирование данных в хранилище BLOB-объектов Azure и обратно с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1](v1/data-factory-azure-blob-connector.md)
> * [Текущая версия](connector-azure-blob-storage.md)

В этой статье объясняется, как с помощью действия копирования в фабрике данных Azure копировать данные в хранилище BLOB-объектов Azure и обратно. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно копировать из любого поддерживаемого исходного хранилища данных в хранилище BLOB-объектов. Вы также можете копировать данные из хранилища BLOB-объектов в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md).

В частности, этот соединитель хранилища BLOB-объектов поддерживает:

- Копирование больших двоичных объектов в учетные записи хранения Azure общего назначения, в хранилище горячих и холодных BLOB-объектов и из них. 
- Копирование BLOB-объектов с помощью ключа учетной записи, проверка подлинности ресурсов Azure на основе подписанного URL-адреса, субъект-службы или управляемых удостоверений.
- Копирование больших двоичных объектов из блочных, добавочных или страничных BLOB-объектов, а также копирование данных только в блочные BLOB-объекты.
- Копирование больших двоичных объектов "как есть", анализ или создание больших двоичных объектов путем использования [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Если вы включаете _«Разрешить доверенные службы Майкрософт для доступа к этой учетной записи хранения»_ параметр на параметры брандмауэра службы хранилища Azure, в среду выполнения интеграции Azure для подключения к хранилищу больших двоичных объектов завершится ошибкой с ошибку с запретом, так как ADF не рассматриваются как доверенные службы Майкрософт. Можно вместо этого подключаются через локальную среду выполнения интеграции.

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
>HDInsights, Машинное обучение Azure и загрузка Хранилища данных SQL Azure с помощью PolyBase поддерживают только проверку подлинности ключа учетной записи хранилища BLOB-объектов Azure.

### <a name="account-key-authentication"></a>Проверка подлинности на основе ключа учетной записи

При использовании проверки подлинности на основе ключа учетной записи поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойству type должно быть присвоено значение **AzureBlobStorage** (рекомендуется) или **AzureStorage** (см. примечания ниже). |Yes |
| connectionString | В свойстве connectionString указываются сведения, необходимые для подключения к службе хранилища. <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы можете также поместить ключ учетной записи в Azure Key Vault и извлечь конфигурацию `accountKey` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

>[!NOTE]
>При использовании связанной службы тип AzureStorage по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый тип связанной службы — AzureBlobStorage.

**Пример.**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
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
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
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
>- Сейчас Фабрика данных поддерживает как **подписанные URL-адреса уровня службы**, так и **подписанные URL-адреса уровня учетной записи**. Сведения об этих двух типах и способах их создания см. в разделе [Типы подписанных URL-адресов](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures).
>- В более поздней конфигурации набора данных путь к папке — это полный путь, начинающийся с уровня контейнера. Вам нужно настроить такой путь, соответствующий пути в вашем URI SAS.

> [!TIP]
> Чтобы создать подписанный URL-адрес уровня службы для учетной записи хранения, можно выполнить следующие команды PowerShell. Замените заполнители и предоставьте необходимое разрешение.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

При использовании аутентификации SAS поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойству type должно быть присвоено значение **AzureBlobStorage** (рекомендуется) или **AzureStorage** (см. примечания ниже). |Yes |
| sasUri | Укажите URI подписанного URL-адреса для ресурсов службы хранилища, например для большого двоичного объекта или контейнера. <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Маркер SAS также можно поместить в хранилище ключей Azure использовать автоматическая смена и удаление части токена. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

>[!NOTE]
>При использовании связанной службы тип AzureStorage по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый тип связанной службы — AzureBlobStorage.

**Пример.**

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

2. Предоставьте правильное разрешение субъекта-службы в Хранилище BLOB-объектов Azure. Дополнительные сведения о ролях см. в статье [Управление правами доступа к данным службы хранилища Azure с помощью ролей RBAC (предварительная версия)](../storage/common/storage-auth-aad-rbac.md).

    - В Системе управления идентификацией и доступом (IAM) **источнику** необходимо выделить роль не ниже **чтения данных больших двоичных объектов хранилища**.
    - В Системе управления идентификацией и доступом (IAM) **приемнику** необходимо выделить роль не ниже **участника для данных больших двоичных объектов хранилища**.

Для связанной службы хранилища BLOB-объектов Azure поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **AzureBlobStorage**. |Yes |
| serviceEndpoint | Укажите конечную точку хранилища больших двоичных объектов Azure с шаблоном `https://<accountName>.blob.core.windows.net/`. |Yes |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Yes |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

>[!NOTE]
>Проверка подлинности субъекта-службы поддерживается только таким типом связанной службы, как AzureBlobStorage, а не предыдущим типом связанной службы AzureStorage.

**Пример.**

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

### <a name="managed-identity"></a> Управляемые удостоверения для аутентификации ресурсов Azure

Фабрика данных может быть связана с [управляемым удостоверением для ресурсов Azure](data-factory-service-identity.md), которое представляет эту фабрику данных. Это управляемое удостоверение можно использовать непосредственно аналогичны использованию субъекта-службы проверки подлинности хранилища BLOB-объектов. Оно разрешает назначенной фабрике данных обращаться к данным и копировать их из службы хранилища BLOB-объектов и в нее.

Ссылаться на [проверки подлинности при доступе к службе хранилища Azure с помощью Azure Active Directory](../storage/common/storage-auth-aad.md) для проверки подлинности службы хранилища Azure в целом. Чтобы использовать управляемые удостоверения для проверки подлинности ресурсов Azure, выполните следующие действия.

1. [Получить данные идентификации управляемых фабрики данных](data-factory-service-identity.md#retrieve-managed-identity) путем копирования значения «Идентификатор приложения УДОСТОВЕРЕНИЯ службы» созданного вместе с фабрикой.

2. Предоставьте правильное разрешение управляемому удостоверению в Хранилище BLOB-объектов Azure. Дополнительные сведения о ролях см. в статье [Управление правами доступа к данным службы хранилища Azure с помощью ролей RBAC (предварительная версия)](../storage/common/storage-auth-aad-rbac.md).

    - В Системе управления идентификацией и доступом (IAM) **источнику** необходимо выделить роль не ниже **чтения данных больших двоичных объектов хранилища**.
    - В Системе управления идентификацией и доступом (IAM) **приемнику** необходимо выделить роль не ниже **участника для данных больших двоичных объектов хранилища**.

Для связанной службы хранилища BLOB-объектов Azure поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **AzureBlobStorage**. |Yes |
| serviceEndpoint | Укажите конечную точку хранилища больших двоичных объектов Azure с шаблоном `https://<accountName>.blob.core.windows.net/`. |Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

> [!NOTE]
> Проверка подлинности ресурсов Azure с помощью управляемых удостоверений поддерживается только таким типом связанной службы как AzureBlobStorage, но не предыдущим типом связанной службы AzureStorage. 

**Пример.**

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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел содержит список свойств, поддерживаемых набором данных хранилища BLOB-объектов.

Чтобы скопировать данные в хранилище BLOB-объектов и из него, установите тип свойства набора данных **AzureBlob**. Поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство type для набора данных должно иметь значение **AzureBlob**. |Yes |
| folderPath | Путь контейнеру и папке в хранилище BLOB-объектов. <br/><br/>Фильтр с подстановочными знаками поддерживается для пути без имени контейнера. Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br/><br/>Например: myblobcontainer/myblobfolder//. Дополнительные примере см. в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). |Yes для действия Copy/Lookup, No для действия GetMetadata |
| fileName | **Имя или фильтр постановочных знаков** для файлов по указанному folderPath. Если этому свойству не присвоить значение, набор данных будет указывать на все большие двоичные объекты в папке. <br/><br/>Допустимые знаки подстановки для фильтра: `*` (соответствует нулю или нескольким символам) и `?` (соответствует нулю или одному символу).<br/>Пример 1. `"fileName": "*.csv"`<br/>Пример 2. `"fileName": "???20180427.txt"`<br/>Используйте `^` для экранирования символов, если фактическое имя файла содержит подстановочный знак или этот escape-символ.<br/><br/>Если свойство fileName для выходного набора данных не указано, а **preserveHierarchy** не указано в приемнике действия, действие копирования автоматически создаст имя большого двоичного объекта в следующем формате: *Data.[GUID идентификатора выполнения действия].[GUID, если поведение — FlattenHierarchy].[формат, если настроено].[сжатие, если настроено]*. Например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz. При копировании из табличного источника с использованием имени таблицы вместо запроса формат имени будет таким: "*[имя таблицы].[фoрмат].[сжатие, если настроено]*". Например, "MyTable.csv". |Нет  |
| modifiedDatetimeStart | Фильтр файлов на основе атрибута: последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| Нет  |
| modifiedDatetimeEnd | Фильтр файлов на основе атрибута: последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| Нет  |
| свойства | Если требуется скопировать файлы между файловыми хранилищами "как есть" (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Вам может понадобиться анализировать или создавать файлы, имеющие определенный формат. Поддерживаются следующие форматы файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** и **ParquetFormat**. Свойству **type** в разделе **format** необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs.md#text-format), [формате JSON](supported-file-formats-and-compression-codecs.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs.md#orc-format) и [формате Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Optimal** и **Fastest**. |Нет  |

>[!TIP]
>Чтобы скопировать все большие двоичные объекты в папке, укажите только **folderPath**.<br>Чтобы скопировать один большой двоичный объект с заданным именем, укажите **folderPath** с частью папки и **fileName** с именем файла.<br>Чтобы скопировать подмножество больших двоичных объектов в папке, укажите **folderPath** с частью папки и **fileName**, используя фильтр подстановочных знаков. 

**Пример.**

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

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником и приемником хранилища BLOB-объектов.

### <a name="blob-storage-as-a-source-type"></a>Хранилище BLOB-объектов в качестве типа источника

Чтобы скопировать данные из хранилища BLOB-объектов, установите тип источника **BlobSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **BlobSource**. |Yes |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике.<br/>Допустимые значения: **true** (по умолчанию) и **false**. | Нет  |

**Пример.**

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

### <a name="blob-storage-as-a-sink-type"></a>Хранилище BLOB-объектов в качестве типа приемника

Чтобы скопировать данные в хранилище BLOB-объектов, установите тип приемника **BlobSink** в действии копирования. Следующие свойства поддерживаются в разделе **sink**.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство типа приемника действия копирования должно иметь значение **BlobSink**. |Yes |
| copyBehavior | Определяет поведение копирования, когда источником являются файлы из файлового хранилища данных.<br/><br/>Допустимые значения:<br/><b>— PreserveHierarchy (по умолчанию)</b>. Сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><b>— FlattenHierarchy</b>. Все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированные имена. <br/><b>— MergeFiles</b>. Объединяет все файлы из исходной папки в один файл. Если указано имя Blob-объекта или имя файла, то оно присваивается объединенному файлу. В противном случае присваивается автоматически созданное имя файла. | Нет  |

**Пример.**

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

### <a name="folder-and-file-filter-examples"></a>Примеры фильтров папок и файлов

В этом разделе описываются результаты применения фильтров с подстановочными знаками к пути папки и имени файла.

| folderPath | fileName | recursive | Структура исходной папки и результат фильтрации (извлекаются файлы, выделенные **полужирным** шрифтом)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (пусто, используйте по умолчанию) | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `container/Folder*` | (пусто, используйте по умолчанию) | Да | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `container/Folder*` | `*.csv` | Да | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |

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

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
