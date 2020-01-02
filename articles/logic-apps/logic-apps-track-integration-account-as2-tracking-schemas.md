---
title: Схемы отслеживания AS2 для сообщений B2B
description: Создайте схему отслеживания AS2, которая выполняет мониторинг сообщений B2B в учетных записях интеграции Azure Logic Apps с пакетом интеграции Enterprise.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 515d7cfc985ee9929f70de2c862170ff79ae4d60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792807"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Создание схемы отслеживания сообщений AS2 и запросов MDN в учетных записях интеграции для Azure Logic Apps

Схемы отслеживания B2B позволяют выполнять в учетной записи интеграции Azure мониторинг ошибок, успешных операций и свойств сообщений транзакций типа "бизнес — бизнес" (AS2):

* Схема отслеживания сообщений AS2
* Схема отслеживания уведомлений о состоянии сообщений AS2

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
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Свойство | Тип | Описание |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения AS2. (необязательный параметр) |
| receiverPartnerName | Строка | Имя партнера получателя сообщения AS2. (необязательный параметр) |
| as2To | Строка | Имя получателя сообщения AS2 из заголовков сообщения AS2. (обязательный параметр) |
| as2From | Строка | Имя отправителя сообщения AS2 из заголовков сообщения AS2. (обязательный параметр) |
| agreementName | Строка | Имя соглашения AS2, в соответствии с которым разрешаются сообщения. (необязательный параметр) |
| direction | Строка | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| messageId | Строка | Идентификатор сообщения AS2 из заголовков сообщения AS2 (необязательный параметр) |
| dispositionType |Строка | Значение типа метода обработки уведомлений о состоянии сообщения (MDN). (необязательный параметр) |
| fileName | Строка | Имя файла из заголовка сообщения AS2. (необязательный параметр) |
| isMessageFailed |Логический | Указывает, произошел ли сбой сообщения AS2. (обязательный параметр) |
| isMessageSigned | Логический | Указывает, подписано ли сообщение AS2. (обязательный параметр) |
| isMessageEncrypted | Логический | Указывает, зашифровано ли сообщение AS2. (обязательный параметр) |
| isMessageCompressed |Логический | Указывает, сжато ли сообщение AS2. (обязательный параметр) |
| correlationMessageId | Строка | Идентификатор сообщения AS2, используемый для корреляции сообщений с уведомлениями о состоянии сообщений. (необязательный параметр) |
| incomingHeaders |Словарь JToken | Сведения о заголовке входящего сообщения AS2. (необязательный параметр) |
| outgoingHeaders |Словарь JToken | Сведения о заголовке исходящего сообщения AS2. (необязательный параметр) |
| isNrrEnabled | Логический | Если значение неизвестно, то используйте значение по умолчанию. (обязательный параметр) |
| isMdnExpected | Логический | Если значение неизвестно, то используйте значение по умолчанию. (обязательный параметр) |
| mdnType | Перечисление. | Допустимые значения: **NotConfigured**, **Sync** и **Async**. (обязательный параметр) |
||||

## <a name="as2-mdn-tracking-schema"></a>Схема отслеживания уведомлений о состоянии сообщений AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
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

| Свойство | Тип | Описание |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения AS2. (необязательный параметр) |
| receiverPartnerName | Строка | Имя партнера получателя сообщения AS2. (необязательный параметр) |
| as2To | Строка | Имя партнера-получателя сообщения AS2. (обязательный параметр) |
| as2From | Строка | Имя партнера-отправителя сообщения AS2. (обязательный параметр) |
| agreementName | Строка | Имя соглашения AS2, в соответствии с которым разрешаются сообщения. (необязательный параметр) |
| direction |Строка | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| messageId | Строка | Идентификатор сообщения AS2. (необязательный параметр) |
| originalMessageId |Строка | Исходный идентификатор сообщения AS2. (необязательный параметр) |
| dispositionType | Строка | Значение типа метода обработки уведомлений о состоянии сообщения. (необязательный параметр) |
| isMessageFailed |Логический | Указывает, произошел ли сбой сообщения AS2. (обязательный параметр) |
| isMessageSigned |Логический | Указывает, подписано ли сообщение AS2. (обязательный параметр) |
| isNrrEnabled | Логический | Если значение неизвестно, то используйте значение по умолчанию. (обязательный параметр) |
| statusCode | Перечисление. | Допустимые значения: **Accepted**, **Rejected** и **AcceptedWithErrros**. (обязательный параметр) |
| micVerificationStatus | Перечисление. | Допустимые значения: **NotApplicable**, **Succeeded** и **Failed**. (обязательный параметр) |
| correlationMessageId | Строка | Идентификатор корреляции. Исходный идентификатор сообщения (идентификатор сообщения, для которого настроена отправка уведомлений о состоянии сообщения). (необязательный параметр) |
| incomingHeaders | Словарь JToken | Указывает сведения о заголовке входящего сообщения. (необязательный параметр) |
| outgoingHeaders |Словарь JToken | Указывает сведения о заголовке исходящего сообщения. (необязательный параметр) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Схемы отслеживания для протоколов B2B

Сведения о схемах отслеживания для протоколов B2B см. в следующих статьях:

* [Схемы отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Настраиваемые схемы отслеживания B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения см. в статье [Мониторинг и настройка ведения журнала диагностики для взаимодействия B2B в учетных записях интеграции](logic-apps-monitor-b2b-message.md).
* Дополнительные сведения об [отслеживании сообщений B2B в журналах Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)