---
title: выражение Resource () в Azure Monitor запросе журнала | Документация Майкрософт
description: Выражение ресурса используется в Azure Monitor запросе к журналу на основе ресурсов для получения данных из нескольких ресурсов.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624966"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>выражение Resource () в Azure Monitor запросе журнала

`resource`Выражение используется в Azure Monitor запросе, [ограниченном ресурсом](scope.md#query-scope) , для получения данных из других ресурсов. 


## <a name="syntax"></a>Синтаксис

`resource(`*Идентификатор*`)`

## <a name="arguments"></a>Аргументы

- *Идентификатор*: идентификатор ресурса.

| Идентификатор | Описание | Пример
|:---|:---|:---|
| Ресурс | Включает данные для ресурса. | ресурс ("/субскриптионс/кскскскскскскс-кскскскс-кскскскс-кскскскс-кскскскскскскскскскскскс/ресаурцесграупс/миресаурцеграуп/провидерс/Микрософт.компуте/виртуалмачинес/мивм") |
| Группа ресурсов или подписка | Включает данные для ресурса и всех содержащихся в нем ресурсов.  | ресурс ("/субскриптионс/кскскскскскскс-кскскскс-кскскскс-кскскскс-кскскскскскскскскскскскс/ресаурцесграупс/миресаурцеграуп") |


## <a name="notes"></a>Примечания

* Необходимо иметь доступ на чтение к ресурсу.


## <a name="examples"></a>Примеры

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о области запроса см. [в разделе Область запроса журнала и временной диапазон в Azure Monitor log Analytics](scope.md) .
- Ознакомьтесь с полной документацией по [языку запросов Kusto](/azure/kusto/query/).
