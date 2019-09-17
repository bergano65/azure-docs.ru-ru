---
title: Копирование данных из Google AdWords с помощью службы "Фабрика данных Azure" (предварительная версия) | Документация Майкрософт
description: Сведения о копировании данных из Google AdWords в поддерживаемые хранилища данных, используемые в качестве приемников, с помощью действия копирования в конвейере службы "Фабрика данных Azure".
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 0062d2dfb681234ed0b0956d080791db59b2999a
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009274"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory-preview"></a>Копирование данных из Google AdWords с помощью службы "Фабрика данных Azure" (предварительная версия)

Из этой статьи вы узнаете, как с помощью действия копирования в службе "Фабрика данных Azure" копировать данные из Google AdWords. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!IMPORTANT]
> Сейчас этот соединитель доступен в режиме предварительной версии. Попробуйте его и оставьте свой отзыв. Если вы хотите использовать в своем решении зависимость от соединителей в предварительной версии, обратитесь в службу [поддержки Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Google адвордс поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой исходной матрицей](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)


Данные из Google AdWords можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей службы "Фабрика данных", относящихся к соединителю Google AdWords.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Google AdWords поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **GoogleAdWords**. | Да |
| clientCustomerID | Идентификатор клиента учетной записи AdWords, для которой требуется получить данные отчета.  | Да |
| developerToken | Токен разработчика, связанный с учетной записью диспетчера, используемой для предоставления доступа к API AdWords.  Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и разрешить действию копирования ADF передавать его оттуда при копировании данных. Дополнительные сведения см. в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| authenticationType | Механизм OAuth 2.0 для аутентификации. ServiceAuthentication может использоваться только в локальных IR. <br/>Допустимые значения: **ServiceAuthentication**, **UserAuthentication**. | Да |
| refreshtoken | Токен обновления, полученный из Google для авторизации доступа UserAuthentication к AdWords. Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и разрешить действию копирования ADF передавать его оттуда при копировании данных. Дополнительные сведения см. в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Нет |
| clientId | Идентификатор клиента приложения Google, используемый для получения токена обновления. Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и разрешить действию копирования ADF передавать его оттуда при копировании данных. Дополнительные сведения см. в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Нет |
| clientSecret | Секрет клиента приложения Google, используемый для получения токена обновления. Вы можете обозначить это поле как SecureString, чтобы безопасно хранить его в ADF, или сохранить пароль в Azure Key Vault и разрешить действию копирования ADF передавать его оттуда при копировании данных. Дополнительные сведения см. в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Нет |
| email | Идентификатор электронной почты учетной записи службы, используемый для ServiceAuthentication. Может использоваться только в резидентных IR.  | Нет |
| keyFilePath | Полный путь к файлу ключа .p12, используемый для аутентификации адреса электронной почты учетной записи службы. Может использоваться только в резидентных IR.  | Нет |
| trustedCertPath | Полный путь к PEM-файлу, который содержит сертификаты доверенного ЦС для проверки сервера при подключении по протоколу SSL. Это свойство может устанавливаться только при использовании SSL в локальных средах выполнения интеграции. Значением по умолчанию является файл cacerts.pem, который устанавливается вместе с IR.  | Нет |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС из доверенного хранилища системы или из указанного PEM-файла. По умолчанию для этого параметра используется значение false.  | Нет |

**Пример.**

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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Google AdWords.

Чтобы скопировать данные из Google AdWords, установите для свойства type набора данных значение **GoogleAdWordsObject**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **GoogleAdWordsObject**. | Да |
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

Чтобы копировать данные Google AdWords, установите тип источника **GoogleAdWordsSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **GoogleAdWordsSource**. | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Пример.**

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

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Следующие шаги
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
