---
title: Схемы отслеживания AS2 для B2B-сообщений
description: Создавайте схемы отслеживания для мониторинга сообщений AS2 в приложениях логики Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906973"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Создание схем для отслеживания сообщений AS2 в приложениях логики Azure

Схемы отслеживания B2B позволяют выполнять в учетной записи интеграции Azure мониторинг ошибок, успешных операций и свойств сообщений транзакций типа "бизнес — бизнес" (AS2):

* Схема отслеживания сообщений AS2
* As2 Уведомление о отправке сообщений (MDN) схема отслеживания

## <a name="as2-message-tracking-schema"></a>Схема отслеживания сообщений AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Свойство | Обязательно | Тип | Описание |
|----------|----------|------|-------------|
| senderPartnerName | нет | Строка | Имя партнера отправителя сообщения AS2 |
| receiverPartnerName | нет | Строка | Имя партнера получателя сообщений AS2 |
| as2To | Да | Строка | Имя получателя сообщений AS2 из заголовков сообщения AS2 |
| as2From | Да | Строка | Имя отправителя сообщений AS2 из заголовков сообщения AS2 |
| agreementName | нет | Строка | Имя соглашения AS2, в соответствии с которым разрешаются сообщения. |
| direction | Да | Строка | Направление потока сообщений, который `receive` является либо`send` |
| messageId | нет | Строка | Идентификатор сообщения AS2 из заголовков сообщения AS2 |
| dispositionType | нет | Строка | Уведомление о отправке сообщений (MDN) значение типа диспозиции |
| fileName | нет | Строка | Имя файла из заголовка сообщения AS2 |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение AS2 |
| isMessageSigned | Да | Логическое | Подписано ли сообщение AS2 |
| isMessageEncrypted | Да | Логическое | Было ли зашифровано сообщение AS2 |
| isMessageCompressed | Да | Логическое | Сжато ли сообщение AS2 |
| correlationMessageId | нет | Строка | Идентификатор сообщений AS2 для корреляции сообщений с MDN |
| incomingHeaders | нет | Словарь JToken | Входящие детали заголовка сообщения AS2 |
| outgoingHeaders | нет | Словарь JToken | Исходящие детали заголовка сообщений AS2 |
| isNrrEnabled | Да | Логическое | Использовать ли значение по умолчанию, если значение не известно |
| isMdnExpected | Да | Логическое | Использовать ли значение по умолчанию, если значение не известно |
| mdnType | Да | Перечисление. | Разрешенные `NotConfigured`значения: `Sync`,`Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>Схема отслеживания уведомлений о состоянии сообщений AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Свойство | Обязательно | Тип | Описание |
|----------|----------|------|-------------|
| senderPartnerName | нет | Строка | Имя партнера отправителя сообщения AS2 |
| receiverPartnerName | нет | Строка | Имя партнера получателя сообщений AS2 |
| as2To | Да | Строка | Имя партнера, который получает сообщение AS2 |
| as2From | Да | Строка | Имя партнера, отправлявшего сообщение AS2 |
| agreementName | нет | Строка | Имя соглашения AS2, в соответствии с которым разрешаются сообщения. |
| direction | Да | Строка | Направление потока сообщений, который `receive` является либо`send` |
| messageId | нет | Строка | Идентификатор сообщения AS2 |
| originalMessageId | нет | Строка | Идентификатор исходного сообщения AS2 |
| dispositionType | нет | Строка | Значение типа диспозиции MDN |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение AS2 |
| isMessageSigned | Да | Логическое | Подписано ли сообщение AS2 |
| isNrrEnabled | Да | Логическое | Использовать ли значение по умолчанию, если значение не известно |
| statusCode | Да | Перечисление. | Разрешенные `Accepted`значения: `Rejected`,`AcceptedWithErrors` |
| micVerificationStatus | Да | Перечисление. | Разрешенные`NotApplicable`значения: `Succeeded`,`Failed` |
| correlationMessageId | нет | Строка | Идентификатор корреляции, который является идентификатором для исходного сообщения с настройкой MDN |
| incomingHeaders | нет | Словарь JToken | Детали входящего заголовка сообщения |
| outgoingHeaders | нет | Словарь JToken | Сведения о исходящих заголовках сообщений |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Схемы отслеживания для протоколов B2B

Сведения о схемах отслеживания для протоколов B2B см. в следующих статьях:

* [Схемы отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Настраиваемые схемы отслеживания B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Мониторинг сообщений B2B с помощью журналов Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)