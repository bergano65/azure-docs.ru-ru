---
title: Схемы отслеживания X12 для сообщений B2B
description: Создание схем отслеживания для отслеживания сообщений X12 для Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905307"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Создание схем для отслеживания сообщений X12 в Azure Logic Apps

Чтобы помочь вам отслеживать успех, ошибки и свойства сообщений для транзакций "бизнес — бизнес" (B2B), вы можете использовать эти схемы отслеживания X12 в своей учетной записи интеграции:

* схема отслеживания набора транзакций X12;
* Схема отслеживания подтверждения для набора транзакций X12
* Схема отслеживания обмена X12
* Схема отслеживания подтверждения обмена X12
* схема отслеживания функциональной группы X12;
* Схема отслеживания подтверждения для функциональной группы X12

## <a name="x12-transaction-set-tracking-schema"></a>схема отслеживания набора транзакций X12;

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
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| Свойство | Обязательно для заполнения | Тип | Description |
|----------|----------|------|-------------|
| senderPartnerName | Нет | String | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | Нет | String | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | String | Отправить классификатор партнера |
| senderIdentifier | Да | String | Отправить идентификатор партнера |
| receiverQualifier | Да | String | Получить квалификатор партнера |
| receiverIdentifier | Да | String | Получить идентификатор партнера |
| agreementName | Нет | String | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, которое либо `receive`, либо `send` |
| interchangeControlNumber | Нет | String | Контрольный номер обмена |
| functionalGroupControlNumber | Нет | String | Номер функционального элемента управления |
| transactionSetControlNumber | Нет | String | Контрольный номер набора транзакций |
| CorrelationMessageId | Нет | String | Идентификатор сообщения корреляции, который является сочетанием {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} |
| messageType | Нет | String | Набор транзакций или тип документа |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| isTechnicalAcknowledgmentExpected | Да | Логическое | Настроено ли техническое подтверждение в соглашении X12 |
| isFunctionalAcknowledgmentExpected | Да | Логическое | Настроено ли функциональное подтверждение в соглашении X12 |
| needAk2LoopForValidMessages | Да | Логическое | Требуется ли цикл AK2 для допустимого сообщения |
| segmentsCount | Нет | Целое число | Число сегментов в наборе транзакций X12 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>Схема отслеживания подтверждения для набора транзакций X12

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

| Свойство | Обязательно для заполнения | Тип | Description |
|----------|----------|------|-------------|
| senderPartnerName | Нет | String | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | Нет | String | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | String | Отправить классификатор партнера |
| senderIdentifier | Да | String | Отправить идентификатор партнера |
| receiverQualifier | Да | String | Получить квалификатор партнера |
| receiverIdentifier | Да | String | Получить идентификатор партнера |
| agreementName | Нет | String | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, которое либо `receive`, либо `send` |
| interchangeControlNumber | Нет | String | Контрольный номер обмена функционального подтверждения. Значение заполняется только для стороны отправки, когда для сообщений, отправляемых партнеру, получено функциональное подтверждение. |
| functionalGroupControlNumber | Нет | String | Контрольный номер функциональной группы для функционального подтверждения. Значение заполняется только для стороны отправки, когда для сообщений, отправленных партнеру, получено функциональное подтверждение. |
| isaSegment | Нет | String | Сегмент ISA сообщения. Значение заполняется только для стороны отправки, когда для сообщений, отправленных партнеру, получено функциональное подтверждение. |
| gsSegment | Нет | String | Сегмент GS сообщения. Значение заполняется только для стороны отправки, когда для сообщений, отправленных партнеру, получено функциональное подтверждение. |
| respondingfunctionalGroupControlNumber | Нет | String | Контрольный номер обмена в ответе |
| respondingFunctionalGroupId | Нет | String | Идентификатор взаимоотвечающей группы, который сопоставляется с AK101 в подтверждении |
| respondingtransactionSetControlNumber | Нет | String | Контрольный номер набора транзакций в ответе |
| respondingTransactionSetId | Нет | String | Идентификатор набора транзакций, который соответствует AK201 в подтверждении |
| statusCode | Да | Логическое | Код состояния подтверждения набора транзакций |
| segmentsCount | Да | Перечисление. | Код состояния подтверждения с этими допустимыми значениями: `Accepted`, `Rejected`и `AcceptedWithErrors` |
| processingStatus | Да | Перечисление. | Обработка состояния подтверждения с этими допустимыми значениями: `Received`, `Generated`и `Sent` |
| CorrelationMessageId | Нет | String | Идентификатор сообщения корреляции, который является сочетанием {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| ak2Segment | Нет | String | Подтверждение для набора транзакций в полученной функциональной группе |
| ak3Segment | Нет | String | Сообщает об ошибках в сегменте данных |
| ak5Segment | Нет | String | Сообщает, принимается ли или отклоняется набор транзакций, определенный в сегменте AK2, и почему |
|||||

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

| Свойство | Обязательно для заполнения | Тип | Description |
|----------|----------|------|-------------|
| senderPartnerName | Нет | String | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | Нет | String | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | String | Отправить классификатор партнера |
| senderIdentifier | Да | String | Отправить идентификатор партнера |
| receiverQualifier | Да | String | Получить квалификатор партнера |
| receiverIdentifier | Да | String | Получить идентификатор партнера |
| agreementName | Нет | String | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, которое либо `receive`, либо `send` |
| interchangeControlNumber | Нет | String | Контрольный номер обмена |
| isaSegment | Нет | String | Сегмент ISA сообщения |
| isTechnicalAcknowledgmentExpected | Логическое | Настроено ли техническое подтверждение в соглашении X12  |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| isa09 | Нет | String | Дата обмена документом X12 |
| isa10 | Нет | String | Время обмена документом X12 |
| isa11 | Нет | String | Идентификатор стандартов управления обменом X12 |
| isa12 | Нет | String | Номер версии элемента управления X12 Interchange |
| isa14 | Нет | String | Запрошено подтверждение X12 |
| isa15 | Нет | String | Индикатор для тестирования или производства |
| isa16 | Нет | String | Разделитель элементов |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>Схема отслеживания подтверждения обмена X12

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

| Свойство | Обязательно для заполнения | Тип | Description |
|----------|----------|------|-------------|
| senderPartnerName | Нет | String | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | Нет | String | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | String | Отправить классификатор партнера |
| senderIdentifier | Да | String | Отправить идентификатор партнера |
| receiverQualifier | Да | String | Получить квалификатор партнера |
| receiverIdentifier | Да | String | Получить идентификатор партнера |
| agreementName | Нет | String | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, которое либо `receive`, либо `send` |
| interchangeControlNumber | Нет | String | Контрольный номер обмена для технического подтверждения, полученного от партнеров |
| isaSegment | Нет | String | Сегмент ISA для технического подтверждения, полученного от партнеров |
| respondingInterchangeControlNumber | Нет | String | Контрольный номер обмена для технического подтверждения, полученного от партнеров |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| statusCode | Да | Перечисление. | Код состояния подтверждения обмена с этими допустимыми значениями: `Accepted`, `Rejected`и `AcceptedWithErrors` |
| processingStatus | Да | Перечисление. | Состояние подтверждения с этими разрешенными значениями: `Received`, `Generated`и `Sent` |
| ta102 | Нет | String | Дата обмена |
| ta103 | Нет | String | Время обмена |
| ta105 | Нет | String | Код заметки обмена |
|||||

## <a name="x12-functional-group-tracking-schema"></a>схема отслеживания функциональной группы X12;

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

| Свойство | Обязательно для заполнения | Тип | Description |
|----------|----------|------|-------------|
| senderPartnerName | Нет | String | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | Нет | String | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | String | Отправить классификатор партнера |
| senderIdentifier | Да | String | Отправить идентификатор партнера |
| receiverQualifier | Да | String | Получить квалификатор партнера |
| receiverIdentifier | Да | String | Получить идентификатор партнера |
| agreementName | Нет | String | Имя соглашения X12, в которое разрешаются сообщения |
| direction | Да | Перечисление. | Направление потока сообщений: Receive или Send |
| interchangeControlNumber | Нет | String | Контрольный номер обмена |
| functionalGroupControlNumber | Нет | String | Номер функционального элемента управления |
| gsSegment | Нет | String | Сегмент GS сообщений |
| isTechnicalAcknowledgmentExpected | Да | Логическое | Настроено ли техническое подтверждение в соглашении X12 |
| isFunctionalAcknowledgmentExpected | Да | Логическое | Настроено ли функциональное подтверждение в соглашении X12 |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| gs01 | Нет | String | Код функционального идентификатора |
| gs02 | Нет | String | Код отправителя приложения |
| gs03 | Нет | String | Код получателя приложения |
| gs04 | Нет | String | Дата функциональной группы |
| gs05 | Нет | String | Время функциональной группы |
| gs07 | Нет | String | Код ответственного агентства |
| gs08 | Нет | String | Код идентификатора для версии, выпуска или отрасли |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>Схема отслеживания подтверждения для функциональной группы X12

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

| Свойство | Обязательно для заполнения | Тип | Description |
|----------|----------|------|-------------|
| senderPartnerName | Нет | String | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | Нет | String | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | String | Отправить классификатор партнера |
| senderIdentifier | Да | String | Отправить идентификатор партнера |
| receiverQualifier | Да | String | Получить квалификатор партнера |
| receiverIdentifier | Да | String | Получить идентификатор партнера |
| agreementName | Нет | String | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, которое либо `receive`, либо `send` |
| interchangeControlNumber | Нет | String | Контрольный номер обмена, который заполняет сторону отправки при получении технического подтверждения от партнеров |
| functionalGroupControlNumber | Нет | String | Контрольный номер функциональной группы технического подтверждения, который заполняется на стороне отправки при получении технического подтверждения от партнеров. |
| isaSegment | Нет | String | Совпадает с контрольным номером обмена, но заполняется только в конкретных случаях |
| gsSegment | Нет | String | Совпадает с контрольным номером функциональной группы, но заполняется только в конкретных случаях |
| respondingfunctionalGroupControlNumber | Нет | String | Контрольный номер исходной функциональной группы |
| respondingFunctionalGroupId | Нет | String | Сопоставляется с AK101 в ИДЕНТИФИКАТОРе функциональной группы подтверждения |
| isMessageFailed | Логическое | Не удалось ли сообщение X12 |
| statusCode | Да | Перечисление. | Код состояния подтверждения с этими допустимыми значениями: `Accepted`, `Rejected`и `AcceptedWithErrors` |
| processingStatus | Да | Перечисление. | Обработка состояния подтверждения с этими допустимыми значениями: `Received`, `Generated`и `Sent` |
| ak903 | Нет | String | Число полученных наборов транзакций |
| ak904 | Нет | String | Число наборов транзакций, принятых в указанной функциональной группе |
| ak9Segment | Нет | String | Принимается ли или отклоняется функциональная группа, определенная в сегменте AK1 функциональная группа, и почему |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Схемы отслеживания для протоколов B2B

Сведения о схемах отслеживания для протоколов B2B см. в следующих статьях:

* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Настраиваемые схемы отслеживания B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Мониторинг сообщений B2B с помощью журналов Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)