---
title: Хранение учетных данных в Azure Key Vault
description: Узнайте, как хранить учетные данные для хранилищ данных, используемых в Azure Key Vault, которые фабрика данных Azure может автоматически извлекать в среду выполнения.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 22ab4433d84db926733fd0b18035875e63322dda
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451692"
---
# <a name="store-credential-in-azure-key-vault"></a>Хранение учетных данных в Azure Key Vault

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Учетные данные для хранилищ данных и вычислительных ресурсов можно хранить в [Azure Key Vault](../key-vault/general/overview.md). Фабрика данных Azure извлекает учетные данные при выполнении действия, которое использует хранилище данных или вычислительный ресурс.

Сейчас эта функция поддерживается для всех видов действий, кроме пользовательских действий. Дополнительные сведения о настройке соединителя см. в разделе "Свойства связанной службы" [в статьях, посвященных каждому типу соединителей](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="prerequisites"></a>Предварительные требования

Эта функция основана на управляемой итоге фабрики данных. Узнайте, как это работает на [управляемой идентификации для фабрики data,](data-factory-service-identity.md) и убедитесь, что на фабрике данных есть ассоциированный.

## <a name="steps"></a>Шаги

Чтобы указать учетные данные, хранимые в Azure Key Vault, необходимо:

1. **Retrieve data factory managed identity** by copying the value of "Managed Identity Object ID" generated along with your factory. При использовании ui-единого использования aDF-автора идентификатор объекта управляемого идентификатора идентификации будет отображаться в окне создания подключенных услуг Azure Key Vault; вы также можете получить его с портала Azure, обратитесь к [управляемой идентификации фабрики retrieve.](data-factory-service-identity.md#retrieve-managed-identity)
2. **Предоставьте управляемый доступ к ключу доступа к вашему Убежище юков Azure.** В хранилище ключей -> политикдоступа - > добавить политику доступа, поиск этой управляемой идентификации, чтобы предоставить **Разрешение** при отсечении разрешений. Это позволит указанной фабрике получить доступ к секрету в хранилище ключей.
3. **Создайте связанную службу, указывающую на хранилище ключей Azure.** Дополнительные сведения см. в разделе [Связанная служба Azure Key Vault](#azure-key-vault-linked-service).
4. **Создайте службу, связанную с хранилищем данных, внутри которой ссылается соответствующая тайна, хранящаяся в хранилище ключей.** См. дополнительные сведения об [указании секрета, хранящегося в хранилище ключей](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Связанная служба Azure Key Vault

Для связанной службы Azure Key Vault поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **AzureKeyVault**. | Да |
| BaseUrl | Укажите URL-адрес Azure Key Vault. | Да |

**С помощью пользовательского интерфейса для создания:**

Выберите **подключенные** -> **службы** -> **связи Новые**. В новой связанной службе поиск и выбор "Azure Key Vault":

![Поиск в убежище ключей Azure](media/store-credentials-in-key-vault/search-akv.png)

Выберите подготовленное хранилище ключей Azure, в котором хранятся ваши учетные данные. Вы можете выполнить **тестовое подключение**, чтобы убедиться, что ваше подключение AKV действительно. 

![Настройка Azure Key Vault](media/store-credentials-in-key-vault/configure-akv.png)

**Пример JSON:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Указание секрета, хранящегося в хранилище ключей

Следующие свойства поддерживаются при настройке поля в связанной службе, указывающей секрет хранилища ключей:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type поля необходимо задать значение **AzureKeyVaultSecret**. | Да |
| secretName | Имя секрета в Убежище ключей Azure. | Да |
| secretVersion | Версия секрета в Убежище ключей Azure.<br/>Если не указано, используется последняя версия секрета.<br/>Если указано, то он придерживается указанной версии.| нет |
| store | Ссылается на связанную службу Azure Key Vault, которая используется для хранения учетных данных. | Да |

**С помощью пользовательского интерфейса для создания:**

Выберите **Azure Key Vault** для секретных полей при создании подключения к своему хранилищу данных или вычислительному ресурсу. Выберите подготовленную связанную службу Azure Key Vault и укажите **имя секрета**. Вы также можете предоставить версию секрета. 

>[!TIP]
>Для разъемов, использующих строку подключения в связанной службе, такой как S'L Server, Blob storage и т.д., вы можете либо хранить только секретное поле, например пароль в AKV, либо хранить всю строку соединения в AKV. Вы можете найти оба варианта на uI.

![Наконфигурните секрет Убежища ключей Azure](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Пример JSON: (см. раздел "пароль") **

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Следующие шаги
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
