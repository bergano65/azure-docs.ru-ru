---
title: Пользовательские схемы отслеживания для сообщений B2B
description: Создание настраиваемых схем отслеживания для отслеживания сообщений B2B в Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903054"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Создание настраиваемых схем отслеживания, отслеживающих сквозные рабочие процессы в Azure Logic A

Azure Logic Apps имеет встроенные возможности отслеживания, которые можно включить для частей рабочего процесса. Однако можно настроить пользовательское отслеживание, которое регистрирует события от начала до конца рабочих процессов, например рабочие процессы, которые включают приложение логики, BizTalk Server, SQL Server или любой другой слой. В этой статье приведен настраиваемый код, который можно использовать в уровнях за пределами приложения логики.

## <a name="custom-tracking-schema"></a>Настраиваемая схема отслеживания

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": ,
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {}
      }
   ]
}
```

| Свойство | Обязательно для заполнения | Тип | Description |
|----------|----------|------|-------------|
| sourceType | Да | String | Тип источника запуска с допустимыми значениями: `Microsoft.Logic/workflows`, `custom` |
| source | Да | String или JToken | Если тип источника — `Microsoft.Logic/workflows`, сведения об источнике должны следовать этой схеме. Если тип источника — `custom`, схема является JToken. |
| systemId | Да | String | Идентификатор системы приложения логики |
| runId | Да | String | Идентификатор запуска приложения логики |
| operationName | Да | String | Имя операции, например действие или триггер |
| repeatItemScopeName | Да | String | Повторение имени элемента, если действие находится внутри `foreach`или цикла `until` |
| repeatItemIndex | Да | Целое число | Указывает, что действие находится в цикле `foreach` или `until` и является повторяющимся индексом элемента. |
| trackingId | Нет | String | ИДЕНТИФИКАТОР отслеживания для корреляции сообщений |
| correlationId | Нет | String | Идентификатор корреляции для корреляции сообщений |
| clientRequestId | Нет | String | Клиент может заполнить это свойство для корреляции сообщений. |
| eventLevel | Да | String | Уровень события |
| eventTime | Да | Дата и время | Время события в формате UTC: *гггг-мм-ddThh: mm: SS. 00000z)* |
| recordType | Да | String | Тип записи Track только с этим разрешенным значением: `custom` |
| запись | Да | JToken | Пользовательский тип записи только с форматом JToken |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Схемы отслеживания для протоколов B2B

Сведения о схемах отслеживания для протоколов B2B см. в следующих статьях:

* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Схемы отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [мониторинге сообщений B2B с помощью журналов Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)