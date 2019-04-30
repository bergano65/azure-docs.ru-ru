---
title: 'Azure Backup: создание хранилищ Служб восстановления с помощью REST API'
description: Управление операциями восстановления и резервного копирования виртуальной машины Azure с помощью REST API
services: backup
author: pvrk
manager: shivamg
keywords: REST API; Azure VM backup; Azure VM restore;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 4f18b10ee3f4148badc8e53a9660c9f5c998aef7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646713"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Создание хранилища Служб восстановления Azure с помощью REST API

Шаги по созданию хранилища Служб восстановления Azure с помощью REST API описаны в [этой документации](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate). Этот документ содержит справочные сведения по созданию хранилища с именем "testVault" в регионе "Западная часть США".

Для создания или обновления хранилища Служб восстановления Azure используйте следующую операцию *PUT*.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Создать запрос

Для создания запроса *PUT* требуется параметр `{subscription-id}`. Если у вас несколько подписок, см. раздел [Использование нескольких подписок](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Для ваших ресурсов параметры `{resourceGroupName}` и `{vaultName}` определяются вместе с параметром `api-version`. Для этой статьи используется `api-version=2016-06-01`.

Ниже приведены обязательные заголовки.

| Заголовок запроса   | Описание |
|------------------|-----------------|
| *Content-Type:*  | Обязательный. Задайте значение `application/json`. |
| *Authorization:* | Обязательный. Задайте в качестве значения [допустимый токен доступа](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |

Дополнительные сведения по созданию запросов см. в разделе [Components of a REST API request/response](/rest/api/azure/#components-of-a-rest-api-requestresponse) (Компоненты запроса или ответа REST API).

## <a name="create-the-request-body"></a>Создание текста запроса

Для создания текста запроса используются следующие общие определения.

|ИМЯ  |Обязательно для заполнения  |type  |Описание  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  Необязательный eTag       |
|location     |  Да       |String         |   Расположение ресурса      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Свойства хранилища       |
|sku     |         |  [SKU](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    В этом столбце указан уникальный системный идентификатор для каждого ресурса Azure.     |
|теги     |         | Object        |     Теги ресурсов    |

Обратите внимание, что имена хранилища и группы ресурсов указаны в универсальном коде ресурса (URI) PUT. Текст запроса определяет расположение.

## <a name="example-request-body"></a>Примеры текста запроса

Следующий пример текста используется для создания хранилища в регионе "Западная часть США". Укажите расположение. Номер SKU всегда будет ценовой категории "Стандартный".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Ответы

Есть два успешных ответа для операции по созданию или обновлению хранилища Служб восстановления:

|ИМЯ  |Тип  |Описание  |
|---------|---------|---------|
|200 ОК     |   [Хранилище](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OК        |
|201 Создано     | [Хранилище](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Создано      |

Дополнительные сведения об ответах REST API можно узнать в разделе [Process the response message](/rest/api/azure/#process-the-response-message) (Обработка ответного сообщения).

### <a name="example-response"></a>Пример ответа

Сокращенный ответ *201 Created* (201 Создано), полученный из предыдущего примера текста запроса, показывает, что был назначен *идентификатор* и что для состояния *provisioningState* указано значение *Succeeded*:

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

[Create Azure Recovery Services backup policies using REST API](backup-azure-arm-userestapi-createorupdatepolicy.md) (Создание политик резервного копирования Служб восстановления Azure с помощью REST API)

Дополнительные сведения о REST API Azure см. в следующих документах:

- [Recovery Services](/rest/api/recoveryservices/) (Службы восстановления)
- [Начало работы с Azure REST API](/rest/api/azure/)
