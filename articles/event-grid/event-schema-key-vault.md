---
title: Схема событий Azure Event Grid для Убежища ключей Azure
description: Описывает свойства и схему, предусмотренные для событий Azure Key Vault с помощью Azure Event Grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082870"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Схема событий службы "Сетка событий Azure" для Azure Key Vault (предварительная версия)

В этой статье представлены свойства и схема для событий в [Azure Key Vault](../key-vault/index.yml), в настоящее время в предварительном просмотре. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

## <a name="available-event-types"></a>Доступные типы событий

Учетная запись Azure Key Vault генерирует следующие типы событий:

| Полное название мероприятия | Имя отображения событий | Описание |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | Сертификат Новая версия Создана | Запуск при создании нового сертификата или новой версии сертификата. |
| Microsoft.KeyVault.CertificateNearExpiry | Сертификат вблизи истечения | Запуск, когда текущая версия сертификата вот-вот истечет. (По умолчанию 30 дней до истечения срока действия.) |
| Microsoft.KeyVault.CertificateExpired | Срок действия сертификата истек | Запуск по истечении срока действия сертификата. |
| Microsoft.KeyVault.KeyNewVersionCreated | Создана новая ключевая версия | Срабатывает при создании нового ключа или новой ключевой версии. |
| Microsoft.KeyVault.KeyNearExpiry | Ключевой ближайший срок действия | Сработал, когда текущая версия ключа вот-вот истечет. (По умолчанию 30 дней до истечения срока действия.) |
| Microsoft.KeyVault.KeyExpired | Срок действия ключа | Срабатывает при истечении срока действия ключа. |
| Microsoft.KeyVault.SecretNewVersionCreated | Создана секретная новая версия | Запускается при создании новой секретной или новой секретной версии. |
| Microsoft.KeyVault.SecretNearExpiry | Секрет почти срок годности | Срабатывает, когда текущая версия секрета вот-вот истечет. (По умолчанию 30 дней до истечения срока действия.) |
| Microsoft.KeyVault.SecretExpired | Секретный истек | Срабатывает, когда срок действия секрета истек. |

## <a name="event-examples"></a>Примеры событий

Следующий пример показать схему для **Microsoft.KeyVault.SecretNewVersionCreated**:

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

Событие содержит следующие высокоуровневые данные:

| Свойство | Тип | Описание |
| ---------- | ----------- |---|
| идентификатор | строка | Идентификатор объекта, срабатывающего это событие |
| vaultName | строка | Ключевое имя хранилища объекта, вызвавшее это событие |
| objectType | строка | Тип объекта, срабатывающего это событие |
| objectName | строка | Название объекта, вызвавшее это событие |
| version | строка | Версия объекта, вызвавшее это событие |
| nbf | number | Не-до даты в секундах с 1970-01-01T00:00 "объекта, который вызвал это событие |
| exp | number | Срок годности в секундах с 1970-01-01T00:00 "объекта, который вызвал это событие |


## <a name="next-steps"></a>Дальнейшие действия

* Для введения в Azure Event [What is Event Grid?](overview.md)Grid см.
* Для получения дополнительной информации о том, как [Event Grid subscription schema](subscription-creation-schema.md)создать подписку Azure Event Grid, см.
* Чтобы узнать больше об интеграции Key Vault с Event Grid, см. [Мониторинг ключей Vault с Azure Event Grid (предварительный просмотр)](../key-vault/event-grid-overview.md).
* В учебнике по интеграции Key Vault с Event Grid можно получить уведомления о хранилище ключей с помощью [Azure Event Grid (предварительный просмотр).](../key-vault/event-grid-tutorial.md)
* Дополнительные рекомендации по автоматизации Key Vault и Azure можно посмотреть:
    - [Что такое хранилище ключей Azure?](../key-vault/key-vault-overview.md)
    - [мониторингу Key Vault с помощью службы "Сетка событий Azure" (предварительная версия)](../key-vault/event-grid-overview.md)
    - [Получение уведомлений хранилища ключей и реагирование на них с помощью Сетки событий Azure (предварительная версия)](../key-vault/event-grid-tutorial.md)
    - [Обзор автоматизации Azure](../automation/index.yml)
