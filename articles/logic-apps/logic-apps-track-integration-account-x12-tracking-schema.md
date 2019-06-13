---
title: Схемы отслеживания X12 для сообщений B2B в Azure Logic Apps | Документация Майкрософт
description: Создание схемы отслеживания X12, которая выполняет мониторинг сообщений B2B в учетных записях интеграции Azure Logic Apps с Пакетом интеграции Enterprise
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.date: 01/27/2017
ms.openlocfilehash: 1db324006e1e6332b5fdd8afd28ebed8a32ac707
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845772"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Создание схемы отслеживания сообщений X12 в учетных записях интеграции Azure Logic Apps

Чтобы помочь вам отслеживать успех, ошибки и свойства сообщений для транзакций "бизнес — бизнес" (B2B), вы можете использовать эти схемы отслеживания X12 в своей учетной записи интеграции:

* Схема отслеживания набора транзакций X12
* схема отслеживания подтверждения набора транзакций X12;
* схема отслеживания обмена X12;
* схема отслеживания подтверждения обмена X12;
* схема отслеживания функциональной группы X12;
* схема отслеживания подтверждения для функциональной группы X12.

## <a name="x12-transaction-set-tracking-schema"></a>Схема отслеживания набора транзакций X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| Свойство | type | Описание |
| --- | --- | --- |
| senderPartnerName | String | Имя партнера отправителя сообщения X12. (Дополнительно) |
| receiverPartnerName | String | Имя партнера получателя сообщения X12. (Дополнительно) |
| senderQualifier | String | Отправка квалификатора партнера. (обязательный параметр) |
| senderIdentifier | String | Отправка идентификатора партнера. (обязательный параметр) |
| receiverQualifier | String | Получение квалификатора партнера. (обязательный параметр) |
| receiverIdentifier | String | Получение идентификатора партнера. (обязательный параметр) |
| agreementName | String | Имя соглашения X12, в соответствии с которым разрешаются сообщения. (Дополнительно) |
| direction | Enum | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| interchangeControlNumber | String | Контрольный номер обмена. (Дополнительно) |
| functionalGroupControlNumber | String | Функциональный контрольный номер. (Дополнительно) |
| transactionSetControlNumber | String | Контрольный номер набора транзакций. (Дополнительно) |
| CorrelationMessageId | String | Идентификатор сообщения корреляции. Сочетание свойств {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (Дополнительно) |
| messageType | String | Набор транзакций или тип документа. (Дополнительно) |
| isMessageFailed | Boolean | Указывает, произошел ли сбой сообщения X12. (обязательный параметр) |
| isTechnicalAcknowledgmentExpected | Boolean | Указывает, настроено ли в соглашении X12 техническое подтверждение. (обязательный параметр) |
| isFunctionalAcknowledgmentExpected | Boolean | Указывает, настроено ли в соглашении X12 функциональное подтверждение. (обязательный параметр) |
| needAk2LoopForValidMessages | Boolean | Указывает, требуется ли цикл AK2 для допустимого сообщения. (обязательный параметр) |
| segmentsCount | Integer | Число сегментов в наборе транзакций X12. (Дополнительно) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Схема отслеживания подтверждения набора транзакций X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Свойство | type | Описание |
| --- | --- | --- |
| senderPartnerName | String | Имя партнера отправителя сообщения X12. (Дополнительно) |
| receiverPartnerName | String | Имя партнера получателя сообщения X12. (Дополнительно) |
| senderQualifier | String | Отправка квалификатора партнера. (обязательный параметр) |
| senderIdentifier | String | Отправка идентификатора партнера. (обязательный параметр) |
| receiverQualifier | String | Получение квалификатора партнера. (обязательный параметр) |
| receiverIdentifier | String | Получение идентификатора партнера. (обязательный параметр) |
| agreementName | String | Имя соглашения X12, в соответствии с которым разрешаются сообщения. (Дополнительно) |
| direction | Enum | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| interchangeControlNumber | String | Контрольный номер обмена для функционального подтверждения. Значение заполняется только для отправляющей стороны при получении функционального подтверждения для сообщений, отправленных партнеру. (Дополнительно) |
| functionalGroupControlNumber | String | Контрольный номер функциональной группы для функционального подтверждения. Значение заполняется только для отправляющей стороны при получении функционального подтверждения для сообщений, отправленных партнеру. (Дополнительно) |
| isaSegment | String | Сегмент ISA сообщения. Значение заполняется только для отправляющей стороны при получении функционального подтверждения для сообщений, отправленных партнеру. (Дополнительно) |
| gsSegment | String | Сегмент GS сообщения. Значение заполняется только для отправляющей стороны при получении функционального подтверждения для сообщений, отправленных партнеру. (Дополнительно) |
| respondingfunctionalGroupControlNumber | String | Контрольный номер обмена в ответе. (Дополнительно) |
| respondingFunctionalGroupId | String | Идентификатор функциональной группы в ответе, сопоставляющийся с AK101 в подтверждении. (Дополнительно) |
| respondingtransactionSetControlNumber | String | Контрольный номер набора транзакций в ответе. (Дополнительно) |
| respondingTransactionSetId | String | Идентификатор набора транзакций в ответе, сопоставляющийся с AK201 в подтверждении. (Дополнительно) |
| statusCode | Boolean | Код состояния подтверждения для набора транзакций. (обязательный параметр) |
| segmentsCount | Enum | Код состояния подтверждения. Допустимые значения: **Accepted**, **Rejected** и **AcceptedWithErrros**. (обязательный параметр) |
| processingStatus | Enum | Состояние обработки подтверждения. Допустимые значения: **Received**, **Generated** и **Sent**. (обязательный параметр) |
| CorrelationMessageId | String | Идентификатор сообщения корреляции. Сочетание свойств {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (Дополнительно) |
| isMessageFailed | Boolean | Указывает, произошел ли сбой сообщения X12. (обязательный параметр) |
| ak2Segment | String | Подтверждение для набора транзакций в пределах полученной функциональной группы. (Дополнительно) |
| ak3Segment | String | Сообщает об ошибках в сегменте данных. (Дополнительно) |
| ak5Segment | String | Сообщает о том, принят ли определенный в сегменте AK2 набор транзакций, а также сообщает соответствующую причину. (Дополнительно) |
||||

## <a name="x12-interchange-tracking-schema"></a>Схема отслеживания обмена X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| Свойство | type | Описание |
| --- | --- | --- |
| senderPartnerName | String | Имя партнера отправителя сообщения X12. (Дополнительно) |
| receiverPartnerName | String | Имя партнера получателя сообщения X12. (Дополнительно) |
| senderQualifier | String | Отправка квалификатора партнера. (обязательный параметр) |
| senderIdentifier | String | Отправка идентификатора партнера. (обязательный параметр) |
| receiverQualifier | String | Получение квалификатора партнера. (обязательный параметр) |
| receiverIdentifier | String | Получение идентификатора партнера. (обязательный параметр) |
| agreementName | String | Имя соглашения X12, в соответствии с которым разрешаются сообщения. (Дополнительно) |
| direction | Enum | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| interchangeControlNumber | String | Контрольный номер обмена. (Дополнительно) |
| isaSegment | String | Сегмент ISA сообщения. (Дополнительно) |
| isTechnicalAcknowledgmentExpected | Boolean | Указывает, настроено ли в соглашении X12 техническое подтверждение. (обязательный параметр) |
| isMessageFailed | Boolean | Указывает, произошел ли сбой сообщения X12. (обязательный параметр) |
| isa09 | String | Дата обмена документа X12. (Дополнительно) |
| isa10 | String | Время обмена документа X12. (Дополнительно) |
| isa11 | String | Идентификатор стандартов управления обмена X12. (Дополнительно) |
| isa12 | String | Контрольный номер версии обмена X12. (Дополнительно) |
| isa14 | String | Запрос подтверждения X12. (Дополнительно) |
| isa15 | String | Индикатор для тестирования или работы. (Дополнительно) |
| isa16 | String | Разделитель элементов. (Дополнительно) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Схема отслеживания подтверждения обмена X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| Свойство | type | Описание |
| --- | --- | --- |
| senderPartnerName | String | Имя партнера отправителя сообщения X12. (Дополнительно) |
| receiverPartnerName | String | Имя партнера получателя сообщения X12. (Дополнительно) |
| senderQualifier | String | Отправка квалификатора партнера. (обязательный параметр) |
| senderIdentifier | String | Отправка идентификатора партнера. (обязательный параметр) |
| receiverQualifier | String | Получение квалификатора партнера. (обязательный параметр) |
| receiverIdentifier | String | Получение идентификатора партнера. (обязательный параметр) |
| agreementName | String | Имя соглашения X12, в соответствии с которым разрешаются сообщения. (Дополнительно) |
| direction | Enum | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| interchangeControlNumber | String | Контрольный номер обмена для технического подтверждения, полученного от партнеров. (Дополнительно) |
| isaSegment | String | Сегмент ISA для технического подтверждения, полученного от партнеров. (необязательный параметр) |
| respondingInterchangeControlNumber |String | Контрольный номер обмена для технического подтверждения, полученного от партнеров. (Дополнительно) |
| isMessageFailed | Boolean | Указывает, произошел ли сбой сообщения X12. (обязательный параметр) |
| statusCode | Enum | Код состояния подтверждения обмена. Допустимые значения: **Accepted**, **Rejected** и **AcceptedWithErrros**. (обязательный параметр) |
| processingStatus | Enum | Состояние подтверждения. Допустимые значения: **Received**, **Generated** и **Sent**. (обязательный параметр) |
| ta102 | String | Дата обмена. (Дополнительно) |
| ta103 | String | Время обмена. (Дополнительно) |
| ta105 | String | Код примечания обмена. (Дополнительно) |
||||

## <a name="x12-functional-group-tracking-schema"></a>Схема отслеживания функциональной группы X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| Свойство | type | Описание |
| --- | --- | --- |
| senderPartnerName | String | Имя партнера отправителя сообщения X12. (Дополнительно) |
| receiverPartnerName | String | Имя партнера получателя сообщения X12. (Дополнительно) |
| senderQualifier | String | Отправка квалификатора партнера. (обязательный параметр) |
| senderIdentifier | String | Отправка идентификатора партнера. (обязательный параметр) |
| receiverQualifier | String | Получение квалификатора партнера. (обязательный параметр) |
| receiverIdentifier | String | Получение идентификатора партнера. (обязательный параметр) |
| agreementName | String | Имя соглашения X12, в соответствии с которым разрешаются сообщения. (Дополнительно) |
| direction | Enum | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| interchangeControlNumber | String | Контрольный номер обмена. (Дополнительно) |
| functionalGroupControlNumber | String | Функциональный контрольный номер. (Дополнительно) |
| gsSegment | String | Сегмент GS сообщения. (Дополнительно) |
| isTechnicalAcknowledgmentExpected | Boolean | Указывает, настроено ли в соглашении X12 техническое подтверждение. (обязательный параметр) |
| isFunctionalAcknowledgmentExpected | Boolean | Указывает, настроено ли в соглашении X12 функциональное подтверждение. (обязательный параметр) |
| isMessageFailed | Boolean | Указывает, произошел ли сбой сообщения X12. (обязательный параметр)|
| gs01 | String | Код функционального идентификатора. (Дополнительно) |
| gs02 | String | Код отправителя приложения. (Дополнительно) |
| gs03 | String | Код получателя приложения. (Дополнительно) |
| gs04 | String | Дата функциональной группы. (Дополнительно) |
| gs05 | String | Время функциональной группы. (Дополнительно) |
| gs07 | String | Код ответственного агентства. (Дополнительно) |
| gs08 | String | Код версии, выпуска и промышленного идентификатора. (Дополнительно) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>схема отслеживания подтверждения для функциональной группы X12.

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| Свойство | type | Описание |
| --- | --- | --- |
| senderPartnerName | String | Имя партнера отправителя сообщения X12. (Дополнительно) |
| receiverPartnerName | String | Имя партнера получателя сообщения X12. (Дополнительно) |
| senderQualifier | String | Отправка квалификатора партнера. (обязательный параметр) |
| senderIdentifier | String | Отправка идентификатора партнера. (обязательный параметр) |
| receiverQualifier | String | Получение квалификатора партнера. (обязательный параметр) |
| receiverIdentifier | String | Получение идентификатора партнера. (обязательный параметр) |
| agreementName | String | Имя соглашения X12, в соответствии с которым разрешаются сообщения. (Дополнительно) |
| direction | Enum | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| interchangeControlNumber | String | Контрольный номер обмена, который заполняется для отправляющей стороны при получении технического подтверждения от партнеров. (Дополнительно) |
| functionalGroupControlNumber | String | Контрольный номер функциональной группы технического подтверждения, который заполняется для отправляющей стороны при получении технического подтверждения от партнеров. (Дополнительно) |
| isaSegment | String | Как и контрольный номер обмена, но заполняется только в определенных случаях. (Дополнительно) |
| gsSegment | String | Как и контрольный номер функциональной группы, но заполняется только в определенных случаях. (Дополнительно) |
| respondingfunctionalGroupControlNumber | String | Контрольный номер исходной функциональной группы. (Дополнительно) |
| respondingFunctionalGroupId | String | Сопоставляется с AK101 в идентификаторе функциональной группы подтверждения. (Дополнительно) |
| isMessageFailed | Boolean | Указывает, произошел ли сбой сообщения X12. (обязательный параметр) |
| statusCode | Enum | Код состояния подтверждения. Допустимые значения: **Accepted**, **Rejected** и **AcceptedWithErrros**. (обязательный параметр) |
| processingStatus | Enum | Состояние обработки подтверждения. Допустимые значения: **Received**, **Generated** и **Sent**. (обязательный параметр) |
| ak903 | String | Число полученных наборов транзакций. (Дополнительно) |
| ak904 | String | Число наборов транзакций, принятых в указанной функциональной группе. (Дополнительно) |
| ak9Segment | String | Указывает, принята ли определенная в сегменте AK1 функциональная группа, а также сообщает соответствующую причину. (Дополнительно) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Схемы отслеживания для протоколов B2B

Сведения о схемах отслеживания для протоколов B2B см. в следующих статьях:

* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Настраиваемые схемы отслеживания B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [мониторинге сообщений B2B](logic-apps-monitor-b2b-message.md).
* Дополнительные сведения о [отслеживание сообщений B2B в Azure Monitor журналы](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
