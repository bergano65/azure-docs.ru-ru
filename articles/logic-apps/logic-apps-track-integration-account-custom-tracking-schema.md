---
title: Настраиваемые схемы отслеживания для сообщений B2B в Azure Logic Apps | Документация Майкрософт
description: Создайте настраиваемую схему отслеживания, которая выполняет мониторинг сообщений B2B в учетных записях интеграции Azure Logic Apps с пакетом интеграции Enterprise.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.date: 01/27/2017
ms.openlocfilehash: 1e75e0be5404ca7107d3f3201d248088b48da12c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067895"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Создание настраиваемых схем отслеживания, которые отслеживают законченное решение рабочих процессов в Azure Logic Apps

Доступна встроенная функция отслеживания, которую можно включить для различных частей рабочего процесса B2B, например для отслеживания сообщений AS2 или X12. При создании рабочих процессов, содержащих приложение логики, BizTalk Server, SQL Server или любой другой уровень, можно включить настраиваемое отслеживание, которое ведет журнал событий от начала до конца рабочего процесса. 

В этой статье приведен настраиваемый код, который можно использовать в уровнях за пределами приложения логики. 

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
         "repeatItemIndex": "",
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
         "record": {                
         }
      }
   ]
}
```

| Свойство | type | Описание |
| --- | --- | --- |
| sourceType |   | Тип источника выполнения. Допустимые значения — **Microsoft.Logic/workflows** или **custom**. (обязательный параметр) |
| source |   | Если тип источника — **Microsoft.Logic/workflows**, то сведения источника должны следовать этой схеме. Если тип источника — **custom**, то используется схема JToken. (обязательный параметр) |
| systemId | String | Системный идентификатор приложения логики. (обязательный параметр) |
| runId | String | Идентификатор выполнения приложения логики. (обязательный параметр) |
| operationName | String | Имя операции (например, действие или триггер). (обязательный параметр) |
| repeatItemScopeName | String | Повторяет имя элемента, если действие находится внутри цикла `foreach`/ или `until`. (обязательный параметр) |
| repeatItemIndex | Целое число | Определяет, находится ли действие внутри цикла `foreach`/ или `until`. Указывает индекс повторяющегося элемента. (обязательный параметр) |
| trackingId | String | Идентификатор отслеживания для корреляции сообщений. (Дополнительно) |
| correlationId | String | Идентификатор корреляции для корреляции сообщений. (Дополнительно) |
| clientRequestId | String | Клиент может включить его для корреляции сообщений. (Дополнительно) |
| eventLevel |   | Уровень события. (обязательный параметр) |
| eventTime |   | Время события в формате UTC (ГГГГ-ММ-ДДTЧЧ:ММ:СС.00000Z). (обязательный параметр) |
| recordType |   | Тип записи отслеживания. Допустимое значение — **custom**. (обязательный параметр) |
| record |   | Настраиваемый тип записи. Разрешен формат JToken. (обязательный параметр) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Схемы отслеживания для протоколов B2B

Сведения о схемах отслеживания для протоколов B2B см. в следующих статьях:

* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Схемы отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [мониторинге сообщений B2B](logic-apps-monitor-b2b-message.md)
* Дополнительные сведения о [отслеживание сообщений B2B в журналах Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
