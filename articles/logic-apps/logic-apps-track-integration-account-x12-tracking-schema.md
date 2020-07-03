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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
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

| Свойство | Обязательно | Тип | Описание |
|----------|----------|------|-------------|
| senderPartnerName | Нет | Строка | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | Нет | Строка | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | Строка | Отправить классификатор партнера |
| senderIdentifier | Да | Строка | Отправить идентификатор партнера |
| receiverQualifier | Да | Строка | Получить квалификатор партнера |
| receiverIdentifier | Да | Строка | Получить идентификатор партнера |
| agreementName | Нет | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, `receive` либо`send` |
| interchangeControlNumber | Нет | Строка | Контрольный номер обмена |
| functionalGroupControlNumber | Нет | Строка | Номер функционального элемента управления |
| transactionSetControlNumber | Нет | Строка | Номер управления набором транзакций |
| CorrelationMessageId | Нет | Строка | Идентификатор сообщения корреляции, который является сочетанием {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} |
| messageType | Нет | Строка | Набор транзакций или тип документа |
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

| Свойство | Обязательно | Тип | Описание |
|----------|----------|------|-------------|
| senderPartnerName | Нет | Строка | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | Нет | Строка | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | Строка | Отправить классификатор партнера |
| senderIdentifier | Да | Строка | Отправить идентификатор партнера |
| receiverQualifier | Да | Строка | Получить квалификатор партнера |
| receiverIdentifier | Да | Строка | Получить идентификатор партнера |
| agreementName | Нет | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, `receive` либо`send` |
| interchangeControlNumber | Нет | Строка | Контрольный номер обмена функционального подтверждения. Значение заполняется только для стороны отправки, когда для сообщений, отправляемых партнеру, получено функциональное подтверждение. |
| functionalGroupControlNumber | Нет | Строка | Контрольный номер функциональной группы для функционального подтверждения. Значение заполняется только для стороны отправки, когда для сообщений, отправленных партнеру, получено функциональное подтверждение. |
| isaSegment | Нет | Строка | Сегмент ISA сообщения. Значение заполняется только для стороны отправки, когда для сообщений, отправленных партнеру, получено функциональное подтверждение. |
| gsSegment | Нет | Строка | Сегмент GS сообщения. Значение заполняется только для стороны отправки, когда для сообщений, отправленных партнеру, получено функциональное подтверждение. |
| respondingfunctionalGroupControlNumber | Нет | Строка | Контрольный номер обмена в ответе |
| respondingFunctionalGroupId | Нет | Строка | Идентификатор взаимоотвечающей группы, который сопоставляется с AK101 в подтверждении |
| respondingtransactionSetControlNumber | Нет | Строка | Контрольный номер набора транзакций в ответе |
| respondingTransactionSetId | Нет | Строка | Идентификатор набора транзакций, который соответствует AK201 в подтверждении |
| statusCode | Да | Логическое | Код состояния подтверждения набора транзакций |
| segmentsCount | Да | Перечисление. | Код состояния подтверждения с этими допустимыми значениями: `Accepted`, `Rejected`и`AcceptedWithErrors` |
| processingStatus | Да | Перечисление. | Обработка состояния подтверждения с этими разрешенными значениями: `Received`, и `Generated``Sent` |
| CorrelationMessageId | Нет | Строка | Идентификатор сообщения корреляции, который является сочетанием {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| ak2Segment | Нет | Строка | Подтверждение для набора транзакций в полученной функциональной группе |
| ak3Segment | Нет | Строка | Сообщает об ошибках в сегменте данных |
| ak5Segment | Нет | Строка | Сообщает, принимается ли или отклоняется набор транзакций, определенный в сегменте AK2, и почему |
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

| Свойство | Обязательно | Тип | Описание |
|----------|----------|------|-------------|
| senderPartnerName | Нет | Строка | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | Нет | Строка | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | Строка | Отправить классификатор партнера |
| senderIdentifier | Да | Строка | Отправить идентификатор партнера |
| receiverQualifier | Да | Строка | Получить квалификатор партнера |
| receiverIdentifier | Да | Строка | Получить идентификатор партнера |
| agreementName | Нет | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, `receive` либо`send` |
| interchangeControlNumber | Нет | Строка | Контрольный номер обмена |
| isaSegment | Нет | Строка | Сегмент ISA сообщения |
| isTechnicalAcknowledgmentExpected | Логическое значение | Настроено ли техническое подтверждение в соглашении X12  |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| isa09 | Нет | Строка | Дата обмена документом X12 |
| isa10 | Нет | Строка | Время обмена документом X12 |
| isa11 | Нет | Строка | Идентификатор стандартов управления обменом X12 |
| isa12 | Нет | Строка | Номер версии элемента управления X12 Interchange |
| isa14 | Нет | Строка | Запрошено подтверждение X12 |
| isa15 | Нет | Строка | Индикатор для тестирования или производства |
| isa16 | Нет | Строка | Разделитель элементов |
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

| Свойство | Обязательно | Тип | Описание |
|----------|----------|------|-------------|
| senderPartnerName | Нет | Строка | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | Нет | Строка | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | Строка | Отправить классификатор партнера |
| senderIdentifier | Да | Строка | Отправить идентификатор партнера |
| receiverQualifier | Да | Строка | Получить квалификатор партнера |
| receiverIdentifier | Да | Строка | Получить идентификатор партнера |
| agreementName | Нет | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, `receive` либо`send` |
| interchangeControlNumber | Нет | Строка | Контрольный номер обмена для технического подтверждения, полученного от партнеров |
| isaSegment | Нет | Строка | Сегмент ISA для технического подтверждения, полученного от партнеров |
| respondingInterchangeControlNumber | Нет | Строка | Контрольный номер обмена для технического подтверждения, полученного от партнеров |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| statusCode | Да | Перечисление. | Код состояния подтверждения обмена с этими допустимыми значениями: `Accepted`, `Rejected`и`AcceptedWithErrors` |
| processingStatus | Да | Перечисление. | Состояние подтверждения с этими разрешенными значениями: `Received`, `Generated`и`Sent` |
| ta102 | Нет | Строка | Дата обмена |
| ta103 | Нет | Строка | Время обмена |
| ta105 | Нет | Строка | Код заметки обмена |
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

| Свойство | Обязательно | Тип | Описание |
|----------|----------|------|-------------|
| senderPartnerName | Нет | Строка | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | Нет | Строка | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | Строка | Отправить классификатор партнера |
| senderIdentifier | Да | Строка | Отправить идентификатор партнера |
| receiverQualifier | Да | Строка | Получить квалификатор партнера |
| receiverIdentifier | Да | Строка | Получить идентификатор партнера |
| agreementName | Нет | Строка | Имя соглашения X12, в которое разрешаются сообщения |
| direction | Да | Перечисление. | Направление потока сообщений: Receive или Send |
| interchangeControlNumber | Нет | Строка | Контрольный номер обмена |
| functionalGroupControlNumber | Нет | Строка | Номер функционального элемента управления |
| gsSegment | Нет | Строка | Сегмент GS сообщений |
| isTechnicalAcknowledgmentExpected | Да | Логическое | Настроено ли техническое подтверждение в соглашении X12 |
| isFunctionalAcknowledgmentExpected | Да | Логическое | Настроено ли функциональное подтверждение в соглашении X12 |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| gs01 | Нет | Строка | Код функционального идентификатора |
| gs02 | Нет | Строка | Код отправителя приложения |
| gs03 | Нет | Строка | Код получателя приложения |
| gs04 | Нет | Строка | Дата функциональной группы |
| gs05 | Нет | Строка | Время функциональной группы |
| gs07 | Нет | Строка | Код ответственного агентства |
| gs08 | Нет | Строка | Код идентификатора для версии, выпуска или отрасли |
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

| Свойство | Обязательно | Тип | Описание |
|----------|----------|------|-------------|
| senderPartnerName | Нет | Строка | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | Нет | Строка | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | Строка | Отправить классификатор партнера |
| senderIdentifier | Да | Строка | Отправить идентификатор партнера |
| receiverQualifier | Да | Строка | Получить квалификатор партнера |
| receiverIdentifier | Да | Строка | Получить идентификатор партнера |
| agreementName | Нет | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, `receive` либо`send` |
| interchangeControlNumber | Нет | Строка | Контрольный номер обмена, который заполняет сторону отправки при получении технического подтверждения от партнеров |
| functionalGroupControlNumber | Нет | Строка | Контрольный номер функциональной группы технического подтверждения, который заполняется на стороне отправки при получении технического подтверждения от партнеров. |
| isaSegment | Нет | Строка | Совпадает с контрольным номером обмена, но заполняется только в конкретных случаях |
| gsSegment | Нет | Строка | Совпадает с контрольным номером функциональной группы, но заполняется только в конкретных случаях |
| respondingfunctionalGroupControlNumber | Нет | Строка | Контрольный номер исходной функциональной группы |
| respondingFunctionalGroupId | Нет | Строка | Сопоставляется с AK101 в ИДЕНТИФИКАТОРе функциональной группы подтверждения |
| isMessageFailed | Логическое значение | Не удалось ли сообщение X12 |
| statusCode | Да | Перечисление. | Код состояния подтверждения с этими допустимыми значениями: `Accepted`, `Rejected`и`AcceptedWithErrors` |
| processingStatus | Да | Перечисление. | Обработка состояния подтверждения с этими разрешенными значениями: `Received`, и `Generated``Sent` |
| ak903 | Нет | Строка | Число полученных наборов транзакций |
| ak904 | Нет | Строка | Число наборов транзакций, принятых в указанной функциональной группе |
| ak9Segment | Нет | Строка | Принимается ли или отклоняется функциональная группа, определенная в сегменте AK1 функциональная группа, и почему |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Схемы отслеживания для протоколов B2B

Сведения о схемах отслеживания для протоколов B2B см. в следующих статьях:

* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Настраиваемые схемы отслеживания B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие шаги

* [Мониторинг сообщений B2B с помощью журналов Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)