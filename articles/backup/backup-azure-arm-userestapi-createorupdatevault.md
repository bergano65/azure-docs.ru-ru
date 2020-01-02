---
title: создание хранилищ Служб восстановления с помощью REST API
description: Из этой статьи вы узнаете, как управлять операциями резервного копирования и восстановления резервной копии виртуальной машины Azure с помощью REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 1901c35d2b4d8bcd02cc064fcfc844e19969e3b5
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173421"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Создание хранилища Служб восстановления Azure с помощью REST API

Шаги по созданию хранилища Служб восстановления Azure с помощью REST API описаны в [этой документации](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate). Этот документ содержит справочные сведения по созданию хранилища с именем "testVault" в регионе "Западная часть США".

Для создания или обновления хранилища Служб восстановления Azure используйте следующую операцию *PUT*.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Создание запроса

Для создания запроса *PUT* требуется параметр `{subscription-id}`. Если у вас несколько подписок, см. раздел [Использование нескольких подписок](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Для ваших ресурсов параметры `{resourceGroupName}` и `{vaultName}` определяются вместе с параметром `api-version`. Для этой статьи используется `api-version=2016-06-01`.

Ниже приведены обязательные заголовки.

| Заголовок запроса   | ОПИСАНИЕ |
|------------------|-----------------|
| *Content-Type:*  | обязательный параметр. Задайте значение `application/json`. |
| *Authorization:* | обязательный параметр. Задайте в качестве значения `Bearer`допустимый токен доступа[ ](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Дополнительные сведения по созданию запросов см. в разделе [Components of a REST API request/response](/rest/api/azure/#components-of-a-rest-api-requestresponse) (Компоненты запроса или ответа REST API).

## <a name="create-the-request-body"></a>Создание текста запроса

Для создания текста запроса используются следующие общие определения.

|имя  |обязательные  |введите  |ОПИСАНИЕ  |
|---------|---------|---------|---------|
|eTag     |         |   Строка,      |  Необязательный eTag       |
|location     |  true       |Строка,         |   Расположение ресурса      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Свойства хранилища       |
|sku     |         |  [SKU](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    В этом столбце указан уникальный системный идентификатор для каждого ресурса Azure.     |
|Теги     |         | Объект.        |     Теги ресурсов    |

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

|имя  |введите  |ОПИСАНИЕ  |
|---------|---------|---------|
|200 ОК     |   [Хранилище](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | ОК        |
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

## <a name="next-steps"></a>Дополнительная информация

[Create Azure Recovery Services backup policies using REST API](backup-azure-arm-userestapi-createorupdatepolicy.md) (Создание политик резервного копирования Служб восстановления Azure с помощью REST API)

Дополнительные сведения о REST API Azure см. в следующих документах:

- [Recovery Services](/rest/api/recoveryservices/) (Службы восстановления)
- [Начало работы с Azure REST API](/rest/api/azure/)
