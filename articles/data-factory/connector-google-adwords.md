---
title: Копирование данных из Google AdWords
description: Сведения о копировании данных из Google AdWords в поддерживаемые хранилища данных, используемые в качестве приемников, с помощью действия копирования в конвейере службы "Фабрика данных Azure".
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: 79e277cb3d512ee17da866a61e9d6d66a50cd902
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415029"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Копирование данных из Google AdWords с помощью Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Из этой статьи вы узнаете, как с помощью действия копирования в службе "Фабрика данных Azure" копировать данные из Google AdWords. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем Google AdWords поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)


Данные из Google AdWords можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей службы "Фабрика данных", относящихся к соединителю Google AdWords.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Google AdWords поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **GoogleAdWords**. | Да |
| clientCustomerID | Идентификатор клиента учетной записи AdWords, для которой требуется получить данные отчета.  | Да |
| developerToken | Токен разработчика, связанный с учетной записью диспетчера, используемой для предоставления доступа к API AdWords.  Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и разрешить действию копирования ADF передавать его оттуда при копировании данных. Дополнительные сведения см. в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| authenticationType | Механизм OAuth 2.0 для аутентификации. ServiceAuthentication может использоваться только в локальных IR. <br/>Допустимые значения: **ServiceAuthentication**, **UserAuthentication**. | Да |
| refreshtoken | Токен обновления, полученный из Google для авторизации доступа UserAuthentication к AdWords. Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и разрешить действию копирования ADF передавать его оттуда при копировании данных. Дополнительные сведения см. в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | нет |
| clientid | Идентификатор клиента приложения Google, используемого для приобретения маркера обновления. Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и разрешить действию копирования ADF передавать его оттуда при копировании данных. Дополнительные сведения см. в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | нет |
| clientSecret | Секрет клиента приложения Google, используемый для получения токена обновления. Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и разрешить действию копирования ADF передавать его оттуда при копировании данных. Дополнительные сведения см. в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | нет |
| email | Идентификатор электронной почты учетной записи службы, используемый для ServiceAuthentication. Может использоваться только в резидентных IR.  | нет |
| keyFilePath | Полный путь к файлу ключа .p12, используемый для аутентификации адреса электронной почты учетной записи службы. Может использоваться только в резидентных IR.  | нет |
| trustedCertPath | Полный путь файла .pem, содержащий доверенные сертификаты CA для проверки сервера при подключении к TLS. Это свойство может быть установлено только при использовании TLS на самостоятельно мозязенной ИК. Значением по умолчанию является файл cacerts.pem, который устанавливается вместе с IR.  | нет |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС из доверенного хранилища системы или из указанного PEM-файла. Значение по умолчанию — false.  | нет |

**Примере:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных [datasets](concepts-datasets-linked-services.md) для определения наборов данных, см. В этом разделе содержится список свойств, поддерживаемых набором данных Google AdWords.

Чтобы скопировать данные из Google AdWords, установите для свойства type набора данных значение **GoogleAdWordsObject**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено на: **GoogleAdWordsObject** | Да |
| tableName | Имя таблицы. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником данных Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords как источник данных

Чтобы копировать данные Google AdWords, установите тип источника **GoogleAdWordsSource** в действии копирования. Следующие свойства поддерживаются в разделе **источника активности** копирования:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type источника действия копирования необходимо задать значение **GoogleAdWordsSource**. | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например: `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Примере:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
