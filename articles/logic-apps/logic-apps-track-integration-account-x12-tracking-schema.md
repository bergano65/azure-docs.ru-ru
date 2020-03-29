---
title: X12 отслеживания схем для B2B сообщений
description: Создание схем отслеживания для мониторинга сообщений X12 для приложений логики Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905307"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Создание схем для отслеживания сообщений X12 в приложениях логики Azure

Чтобы помочь вам отслеживать успех, ошибки и свойства сообщений для транзакций "бизнес — бизнес" (B2B), вы можете использовать эти схемы отслеживания X12 в своей учетной записи интеграции:

* схема отслеживания набора транзакций X12;
* Схема отслеживания подтверждения транзакций X12
* Схема отслеживания обмена X12
* Схема отслеживания подтверждения обмена X12
* схема отслеживания функциональной группы X12;
* Схема отслеживания функционального подтверждения группы X12

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
| senderPartnerName | нет | Строка | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | нет | Строка | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | Строка | Отправить квалификатор партнера |
| senderIdentifier | Да | Строка | Отправить идентификатор партнера |
| receiverQualifier | Да | Строка | Получение квалификатора партнеров |
| receiverIdentifier | Да | Строка | Получение идентификатора партнера |
| agreementName | нет | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, который `receive` является либо`send` |
| interchangeControlNumber | нет | Строка | Контрольный номер обмена |
| functionalGroupControlNumber | нет | Строка | Функциональный номер управления |
| transactionSetControlNumber | нет | Строка | Номер управления набором транзакций |
| CorrelationMessageId | нет | Строка | Идентификатор корреляционных сообщений, который представляет собой комбинацию «AgreementName»*GroupControlNumber*(TransactionSetControlNumber) |
| messageType | нет | Строка | Набор транзакций или тип документа |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| isTechnicalAcknowledgmentExpected | Да | Логическое | Настроенли ли техническое подтверждение в соглашении X12 |
| isFunctionalAcknowledgmentExpected | Да | Логическое | Настроен ли функциональное подтверждение в соглашении X12 |
| needAk2LoopForValidMessages | Да | Логическое | Требуется ли цикл AK2 для достоверного сообщения |
| segmentsCount | нет | Целое число | Количество сегментов в наборе транзакций X12 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>Схема отслеживания подтверждения транзакций X12

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
| senderPartnerName | нет | Строка | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | нет | Строка | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | Строка | Отправить квалификатор партнера |
| senderIdentifier | Да | Строка | Отправить идентификатор партнера |
| receiverQualifier | Да | Строка | Получение квалификатора партнеров |
| receiverIdentifier | Да | Строка | Получение идентификатора партнера |
| agreementName | нет | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, который `receive` является либо`send` |
| interchangeControlNumber | нет | Строка | Номер управления обменом функционального подтверждения. Значение заполняется только для стороны отправки, где функциональное подтверждение получено для сообщений, отправленных партнеру. |
| functionalGroupControlNumber | нет | Строка | Функциональный номер управления группой функционального подтверждения. Значение заполняется только для стороны отправки, где функциональное подтверждение получено для сообщений, отправленных партнеру |
| isaSegment | нет | Строка | Сегмент ISA сообщения. Значение заполняется только для стороны отправки, где функциональное подтверждение получено для сообщений, отправленных партнеру |
| gsSegment | нет | Строка | Сегмент GS сообщения. Значение заполняется только для стороны отправки, где функциональное подтверждение получено для сообщений, отправленных партнеру |
| respondingfunctionalGroupControlNumber | нет | Строка | Номер управления ответившей развязки |
| respondingFunctionalGroupId | нет | Строка | Ответный функциональный идентификатор группы, который отображает AK101 в подтверждении |
| respondingtransactionSetControlNumber | нет | Строка | Ответтранзакционный номер управления транзакции |
| respondingTransactionSetId | нет | Строка | Ответтранзакционный набор транзакции ID, который отображает ak201 в подтверждении |
| statusCode | Да | Логическое | Код статуса подтверждения набора транзакций |
| segmentsCount | Да | Перечисление. | Код статуса признания с этими `Accepted` `Rejected`допустимыми значениями:`AcceptedWithErrors` |
| processingStatus | Да | Перечисление. | Обработка статуса подтверждения с этими `Received`допустимыми значениями: `Generated``Sent` |
| CorrelationMessageId | нет | Строка | Идентификатор корреляционных сообщений, который представляет собой комбинацию «AgreementName»*GroupControlNumber*(TransactionSetControlNumber) |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| ak2Segment | нет | Строка | Подтверждение транзакции, установленной в пределах полученной функциональной группы |
| ak3Segment | нет | Строка | Отчеты об ошибках в сегменте данных |
| ak5Segment | нет | Строка | Сообщает, принимается или отклонена транзакция, идентифицированная в сегменте AK2, и почему |
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
| senderPartnerName | нет | Строка | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | нет | Строка | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | Строка | Отправить квалификатор партнера |
| senderIdentifier | Да | Строка | Отправить идентификатор партнера |
| receiverQualifier | Да | Строка | Получение квалификатора партнеров |
| receiverIdentifier | Да | Строка | Получение идентификатора партнера |
| agreementName | нет | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, который `receive` является либо`send` |
| interchangeControlNumber | нет | Строка | Контрольный номер обмена |
| isaSegment | нет | Строка | Сегмент сообщения ISA |
| isTechnicalAcknowledgmentExpected | Логическое | Настроенли ли техническое подтверждение в соглашении X12  |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| isa09 | нет | Строка | Дата обмена документами X12 |
| isa10 | нет | Строка | Время обмена документами X12 |
| isa11 | нет | Строка | Идентификатор стандартов управления обменом обмена X12 |
| isa12 | нет | Строка | Номер контрольной версии обмена X12 |
| isa14 | нет | Строка | Запрашивается подтверждение X12 |
| isa15 | нет | Строка | Индикатор для тестирования или производства |
| isa16 | нет | Строка | Элементный сепаратор |
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
| senderPartnerName | нет | Строка | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | нет | Строка | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | Строка | Отправить квалификатор партнера |
| senderIdentifier | Да | Строка | Отправить идентификатор партнера |
| receiverQualifier | Да | Строка | Получение квалификатора партнеров |
| receiverIdentifier | Да | Строка | Получение идентификатора партнера |
| agreementName | нет | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, который `receive` является либо`send` |
| interchangeControlNumber | нет | Строка | Номер контроля за выполнением технического подтверждения, полученного от партнеров |
| isaSegment | нет | Строка | Сегмент ISA для технического подтверждения, полученного от партнеров |
| respondingInterchangeControlNumber | нет | Строка | Номер управления обменом для технического подтверждения, полученного от партнеров |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| statusCode | Да | Перечисление. | Код статуса подтверждения обмена с этими `Accepted` `Rejected`допустимыми значениями:`AcceptedWithErrors` |
| processingStatus | Да | Перечисление. | Статус признания с этими допустимыми `Generated`значениями: `Received``Sent` |
| ta102 | нет | Строка | Дата обмена |
| ta103 | нет | Строка | Время обмена |
| ta105 | нет | Строка | Код заметок обмена |
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
| senderPartnerName | нет | Строка | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | нет | Строка | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | Строка | Отправить квалификатор партнера |
| senderIdentifier | Да | Строка | Отправить идентификатор партнера |
| receiverQualifier | Да | Строка | Получение квалификатора партнеров |
| receiverIdentifier | Да | Строка | Получение идентификатора партнера |
| agreementName | нет | Строка | Название соглашения X12, по которому разрешаются сообщения |
| direction | Да | Перечисление. | Направление потока сообщений, либо получать, либо отправлять |
| interchangeControlNumber | нет | Строка | Контрольный номер обмена |
| functionalGroupControlNumber | нет | Строка | Функциональный номер управления |
| gsSegment | нет | Строка | Сегмент сообщения GS |
| isTechnicalAcknowledgmentExpected | Да | Логическое | Настроенли ли техническое подтверждение в соглашении X12 |
| isFunctionalAcknowledgmentExpected | Да | Логическое | Настроен ли функциональное подтверждение в соглашении X12 |
| isMessageFailed | Да | Логическое | Не удалось ли сообщение X12 |
| gs01 | нет | Строка | Функциональный код идентификатора |
| gs02 | нет | Строка | Код отправителя приложения |
| gs03 | нет | Строка | Код приемника приложения |
| gs04 | нет | Строка | Функциональная дата группы |
| gs05 | нет | Строка | Функциональное групповое время |
| gs07 | нет | Строка | Ответственный код агентства |
| gs08 | нет | Строка | Идентификационный код для версии, выпуска или отрасли |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>Схема отслеживания функционального подтверждения группы X12

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
| senderPartnerName | нет | Строка | Имя партнера отправителя сообщения X12 |
| receiverPartnerName | нет | Строка | Имя партнера получателя сообщений X12 |
| senderQualifier | Да | Строка | Отправить квалификатор партнера |
| senderIdentifier | Да | Строка | Отправить идентификатор партнера |
| receiverQualifier | Да | Строка | Получение квалификатора партнеров |
| receiverIdentifier | Да | Строка | Получение идентификатора партнера |
| agreementName | нет | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. |
| direction | Да | Перечисление. | Направление потока сообщений, который `receive` является либо`send` |
| interchangeControlNumber | нет | Строка | Контрольный номер обмена, который заполняется для стороны отправки, когда техническое подтверждение получено от партнеров |
| functionalGroupControlNumber | нет | Строка | Функциональный номер управления группой технического подтверждения, который заполняет сторону отправки, когда техническое подтверждение получено от партнеров |
| isaSegment | нет | Строка | То же, что и контрольный номер обмена, но населенный только в конкретных случаях |
| gsSegment | нет | Строка | То же, что и функциональный номер управления группой, но населенный только в определенных случаях |
| respondingfunctionalGroupControlNumber | нет | Строка | Номер управления исходной функциональной группы |
| respondingFunctionalGroupId | нет | Строка | Карты к AK101 в идентификаторе функциональной группы подтверждения |
| isMessageFailed | Логическое | Не удалось ли сообщение X12 |
| statusCode | Да | Перечисление. | Код статуса признания с этими `Accepted` `Rejected`допустимыми значениями:`AcceptedWithErrors` |
| processingStatus | Да | Перечисление. | Обработка статуса подтверждения с этими `Received`допустимыми значениями: `Generated``Sent` |
| ak903 | нет | Строка | Количество полученных наборов транзакций |
| ak904 | нет | Строка | Количество наборов транзакций, принятых в идентифицированной функциональной группе |
| ak9Segment | нет | Строка | Принимается или отклонена ли функциональная группа, идентифицированная в сегменте АК1, и почему |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Схемы отслеживания для протоколов B2B

Сведения о схемах отслеживания для протоколов B2B см. в следующих статьях:

* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Настраиваемые схемы отслеживания B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Мониторинг сообщений B2B с помощью журналов Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)