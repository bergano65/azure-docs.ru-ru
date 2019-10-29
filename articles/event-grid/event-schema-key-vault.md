---
title: Схема событий Azure Key Vault службы "Сетка событий Azure"
description: Описание свойств и схемы, предоставленных для Azure Key Vault событий с помощью службы "Сетка событий Azure"
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: c92352dd28b870c5f58dec0b82a8000f14a8e62d
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033528"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Схема событий службы "Сетка событий Azure" для Azure Key Vault (Предварительная версия)

В этой статье приводятся свойства и схема событий [Azure Key Vault](../key-vault/index.yml) , доступных в предварительной версии. См. общие сведения о [схеме событий службы "Сетка событий Azure"](event-schema.md).

## <a name="available-event-types"></a>Доступные типы событий

Учетная запись Azure Key Vault создает следующие типы событий:

| Полное имя события | Отображаемое имя события | Description (Описание) |
| ---------- | ----------- |---|
| Microsoft. KeyVault. Цертификатеневверсионкреатед | Создана новая версия сертификата | Запускается при создании нового сертификата или новой версии сертификата |
| Microsoft. KeyVault. Цертификатенеарекспири | Срок действия сертификата приближается к истечению | Активируется, когда истекает срок действия текущей версии сертификата (по умолчанию 30 дней до истечения срока действия) |
| Microsoft. KeyVault. Цертификатикспиред | Срок действия сертификата истек | Активируется при истечении срока действия сертификата |
| Microsoft. KeyVault. Кэйневверсионкреатед | Создана новая версия | Активируется при создании новой или новой версии ключа |
| Microsoft. KeyVault. Кэйнеарекспири | Ключ приближается к истечению срока действия | Активируется, когда истекает срок действия текущей версии ключа (по умолчанию 30 дней до истечения срока действия) |
| Microsoft. KeyVault. Кэйекспиред | Истек срок действия ключа | Активируется при истечении срока действия ключа |
| Microsoft. KeyVault. Секретневверсионкреатед | Создана секретная новая версия | Активируется при создании новой секретной или новой версии секрета |
| Microsoft. KeyVault. Секретнеарекспири | Срок действия секрета приближается | Активируется, когда истекает срок действия текущей версии секрета (по умолчанию 30 дней до истечения срока действия) |
| Microsoft. KeyVault. Секретекспиред | Срок действия секрета истек | Активируется при истечении срока действия секрета |

## <a name="event-examples"></a>Примеры событий

В следующем примере показана схема для **Microsoft. KeyVault. секретневверсионкреатед**.

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

## <a name="event-properties"></a>Свойства события

Событие содержит следующие основные данные:

| Свойство | Тип | Описание |
| ---------- | ----------- |---|
| id | string | Идентификатор объекта, вызвавшего это событие. |
| vaultName | string | Имя хранилища ключей для объекта, запустившего это событие. |
| objectType | string | Тип объекта, вызвавшего это событие |
| ИмяОбъекта | string | Имя объекта, запустившего это событие |
| версия | string | Версия объекта, вызвавшего это событие |
| nbf | number | Не до даты в секундах с 1970-01-01T00:00:00Z объекта, который инициировал это событие |
| exp | number | Дата окончания срока действия в секундах с 1970-01-01T00:00:00Z объекта, который инициировал это событие |


## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
* Дополнительные сведения об интеграции Key Vault и сетки событий см. в статье [мониторинг Key Vault с помощью службы "Сетка событий Azure" (Предварительная версия)](../key-vault/event-grid-overview.md) .
* Руководство по интеграции Key Vault и службы "Сетка событий" см. в разделе [руководство. маршрутизация Key Vault событий в службу автоматизации Runbook (Предварительная версия)](../key-vault/event-grid-tutorial.md).

- [Обзор Azure Key Vault](../key-vault/key-vault-overview.md)
- [Общие сведения о службе "Сетка событий Azure"](overview.md)
- [Мониторинг Key Vault с помощью службы "Сетка событий Azure" (Предварительная версия)](../key-vault/event-grid-overview.md)
- [Как перенаправлять Key Vault события в модуль Runbook службы автоматизации (Предварительная версия)](../key-vault/event-grid-tutorial.md).
- [Обзор службы автоматизации Azure](../automation/index.yml)
