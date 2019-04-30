---
title: Схемы отслеживания AS2 для сообщений B2B в Azure Logic Apps | Документация Майкрософт
description: Создайте схему отслеживания AS2, которая выполняет мониторинг сообщений B2B в учетных записях интеграции Azure Logic Apps с пакетом интеграции Enterprise.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 180d90450497b38f107f3601944385a003f50282
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845789"
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

| Свойство | type | Описание |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения AS2. (Дополнительно) |
| receiverPartnerName | String | Имя партнера получателя сообщения AS2. (Дополнительно) |
| as2To | String | Имя получателя сообщения AS2 из заголовков сообщения AS2. (обязательный параметр) |
| as2From | String | Имя отправителя сообщения AS2 из заголовков сообщения AS2. (обязательный параметр) |
| agreementName | String | Имя соглашения AS2, в соответствии с которым разрешаются сообщения. (Дополнительно) |
| направление | String | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| messageId | String | Идентификатор сообщения AS2 из заголовков сообщения AS2 (необязательный параметр) |
| dispositionType |String | Значение типа метода обработки уведомлений о состоянии сообщения (MDN). (Дополнительно) |
| fileName | String | Имя файла из заголовка сообщения AS2. (Дополнительно) |
| isMessageFailed |Boolean | Указывает, произошел ли сбой сообщения AS2. (обязательный параметр) |
| isMessageSigned | Boolean | Указывает, подписано ли сообщение AS2. (обязательный параметр) |
| isMessageEncrypted | Boolean | Указывает, зашифровано ли сообщение AS2. (обязательный параметр) |
| isMessageCompressed |Boolean | Указывает, сжато ли сообщение AS2. (обязательный параметр) |
| correlationMessageId | String | Идентификатор сообщения AS2, используемый для корреляции сообщений с уведомлениями о состоянии сообщений. (Дополнительно) |
| incomingHeaders |Словарь JToken | Сведения о заголовке входящего сообщения AS2. (Дополнительно) |
| outgoingHeaders |Словарь JToken | Сведения о заголовке исходящего сообщения AS2. (Дополнительно) |
| isNrrEnabled | Boolean | Если значение неизвестно, то используйте значение по умолчанию. (обязательный параметр) |
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

| Свойство | type | Описание |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения AS2. (Дополнительно) |
| receiverPartnerName | String | Имя партнера получателя сообщения AS2. (Дополнительно) |
| as2To | String | Имя партнера-получателя сообщения AS2. (обязательный параметр) |
| as2From | String | Имя партнера-отправителя сообщения AS2. (обязательный параметр) |
| agreementName | String | Имя соглашения AS2, в соответствии с которым разрешаются сообщения. (Дополнительно) |
| направление |String | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| messageId | String | Идентификатор сообщения AS2. (Дополнительно) |
| originalMessageId |String | Исходный идентификатор сообщения AS2. (Дополнительно) |
| dispositionType | String | Значение типа метода обработки уведомлений о состоянии сообщения. (Дополнительно) |
| isMessageFailed |Boolean | Указывает, произошел ли сбой сообщения AS2. (обязательный параметр) |
| isMessageSigned |Boolean | Указывает, подписано ли сообщение AS2. (обязательный параметр) |
| isNrrEnabled | Boolean | Если значение неизвестно, то используйте значение по умолчанию. (обязательный параметр) |
| statusCode | Перечисление. | Допустимые значения: **Accepted**, **Rejected** и **AcceptedWithErrros**. (обязательный параметр) |
| micVerificationStatus | Перечисление. | Допустимые значения: **NotApplicable**, **Succeeded** и **Failed**. (обязательный параметр) |
| correlationMessageId | String | Идентификатор корреляции. Исходный идентификатор сообщения (идентификатор сообщения, для которого настроена отправка уведомлений о состоянии сообщения). (Дополнительно) |
| incomingHeaders | Словарь JToken | Указывает сведения о заголовке входящего сообщения. (Дополнительно) |
| outgoingHeaders |Словарь JToken | Указывает сведения о заголовке исходящего сообщения. (Дополнительно) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Схемы отслеживания для протоколов B2B

Сведения о схемах отслеживания для протоколов B2B см. в следующих статьях:

* [Схемы отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Настраиваемые схемы отслеживания B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения см. в статье [Мониторинг и настройка ведения журнала диагностики для взаимодействия B2B в учетных записях интеграции](logic-apps-monitor-b2b-message.md).
* Дополнительные сведения о [отслеживание сообщений B2B в журналах Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)