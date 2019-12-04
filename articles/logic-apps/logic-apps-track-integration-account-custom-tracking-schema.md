---
title: Пользовательские схемы отслеживания для сообщений B2B
description: Создайте настраиваемую схему отслеживания, которая выполняет мониторинг сообщений B2B в учетных записях интеграции Azure Logic Apps с пакетом интеграции Enterprise.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 7d7c5ef9e9a86c8b061a56fe41c0c8bbfc5ddbb3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792798"
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

| Свойство | Обязательно для заполнения | Тип | Описание |
| --- | --- | --- | --- |
| sourceType | ДА |   | Тип источника выполнения. Допустимые значения — **Microsoft.Logic/workflows** или **custom**. |
| source | ДА |   | Если тип источника — **Microsoft.Logic/workflows**, то сведения источника должны следовать этой схеме. Если тип источника — **custom**, то используется схема JToken. |
| systemId | ДА | Строка | Системный идентификатор приложения логики. |
| runId | ДА | Строка | Идентификатор выполнения приложения логики. |
| operationName | ДА | Строка | Имя операции (например, действие или триггер). |
| repeatItemScopeName | ДА | Строка | Повторяет имя элемента, если действие находится внутри цикла `foreach`/ или `until`. |
| repeatItemIndex | ДА | Целое число | Определяет, находится ли действие внутри цикла `foreach`/ или `until`. Указывает индекс повторяющегося элемента. |
| trackingId | Нет | Строка | Идентификатор отслеживания для корреляции сообщений. |
| correlationId | Нет | Строка | Идентификатор корреляции для корреляции сообщений. |
| clientRequestId | Нет | Строка | Клиент может включить его для корреляции сообщений. |
| eventLevel | ДА |   | Уровень события. |
| eventTime | ДА |   | Время события в формате UTC (ГГГГ-ММ-ДДTЧЧ:ММ:СС.00000Z). |
| recordType | ДА |   | Тип записи отслеживания. Допустимое значение — **custom**. |
| record | ДА |   | Настраиваемый тип записи. Разрешен формат JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Схемы отслеживания для протоколов B2B

Сведения о схемах отслеживания для протоколов B2B см. в следующих статьях:

* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Схемы отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [мониторинге сообщений B2B](logic-apps-monitor-b2b-message.md)
* Дополнительные сведения об [отслеживании сообщений B2B в журналах Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
