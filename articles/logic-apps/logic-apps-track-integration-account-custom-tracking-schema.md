---
title: Пользовательские схемы отслеживания для B2B сообщений
description: Создавайте пользовательские схемы отслеживания для мониторинга B2B-сообщений в приложениях логики Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903054"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Создавайте пользовательские схемы отслеживания, которые отслеживают сквозные рабочие процессы в Azure Logic A

Azure Logic Apps имеет встроенный слежения, который можно включить для части рабочего процесса. Тем не менее, можно настроить пользовательское отслеживание, которое регистрирует события от начала до конца рабочих процессов, например, рабочие процессы, которые включают логическое приложение, BizTalk Server, S'L Server или любой другой слой. В этой статье приведен настраиваемый код, который можно использовать в уровнях за пределами приложения логики.

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

| Свойство | Обязательно | Тип | Описание |
|----------|----------|------|-------------|
| sourceType | Да | Строка | Тип источника выполнения с этими разрешенными значениями: `Microsoft.Logic/workflows`,`custom` |
| source | Да | Строка или JToken | Если тип источника, `Microsoft.Logic/workflows`исходная информация должна следовать этой схеме. Если тип исхода, `custom`схема JToken. |
| systemId | Да | Строка | Идентификатор системы логических приложений |
| runId | Да | Строка | Логическое приложение запустить id |
| operationName | Да | Строка | Название операции, например, действие или триггер |
| repeatItemScopeName | Да | Строка | Повторите имя элемента, `foreach`если `until` действие находится внутри или цикла |
| repeatItemIndex | Да | Целое число | Означает, что действие `foreach` находится `until` внутри цикла или цикла и является повторным номером индекса элемента. |
| trackingId | нет | Строка | Отслеживание идентификатора для корреляции сообщений |
| correlationId | нет | Строка | Идентификатор корреляции для корреляции сообщений |
| clientRequestId | нет | Строка | Клиент может заполнить это свойство для соотношения сообщений |
| eventLevel | Да | Строка | Уровень мероприятия |
| eventTime | Да | Дата и время | Время проведения мероприятия в формате UTC: *YYYY-MM-DDTHH:MM:SS.00000* |
| recordType | Да | Строка | Тип послужного списка с этим допустимое значение только:`custom` |
| запись | Да | Джокен | Пользовательский тип записи только с форматом JToken |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Схемы отслеживания для протоколов B2B

Сведения о схемах отслеживания для протоколов B2B см. в следующих статьях:

* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Схемы отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие действия

* Подробнее о [мониторинге B2B-сообщений с журналами Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)