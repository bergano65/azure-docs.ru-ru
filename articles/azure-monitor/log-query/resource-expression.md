---
title: выражение Resource () в Azure Monitor запросе журнала | Документация Майкрософт
description: Выражение ресурса используется в Azure Monitor запросе к журналу на основе ресурсов для получения данных из нескольких ресурсов.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: deca6e7ef1c231a82a73067971d86a6e9cdd0599
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817414"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>выражение Resource () в Azure Monitor запросе журнала

Выражение `resource` используется в запросе Azure Monitor, [ограниченном ресурсом](scope.md#query-scope) , для получения данных из других ресурсов. 


## <a name="syntax"></a>Синтаксис

`resource(`*Идентификатор*`)`

## <a name="arguments"></a>Аргументы

- *Идентификатор:* Идентификатор ресурса.

| Идентификатор | Описание | Пример
|:---|:---|:---|
| Resource | Включает данные для ресурса. | ресурс ("/субскриптионс/кскскскскскскс-кскскскс-кскскскс-кскскскс-кскскскскскскскскскскскс/ресаурцесграупс/миресаурцеграуп/провидерс/Микрософт.компуте/виртуалмачинес/мивм") |
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


## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о области запроса см. [в разделе Область запроса журнала и временной диапазон в Azure Monitor log Analytics](scope.md) .
- Ознакомьтесь с полной документацией по [языку запросов Kusto](/azure/kusto/query/).
