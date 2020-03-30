---
title: ресурс () выражение в запросе журнала Azure Monitor Документы Майкрософт
description: Выражение ресурсов используется в ресурсо-ориентированном журнале Azure Monitor для получения данных из нескольких ресурсов.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665705"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>выражение ресурса () в запросе журнала Azure Monitor

Выражение `resource` используется в запросе Azure Monitor, [пригодном для ресурса](scope.md#query-scope) для получения данных из других ресурсов. 


## <a name="syntax"></a>Синтаксис

`resource(`*Идентификатор*`)`

## <a name="arguments"></a>Аргументы

- *Идентификатор*: Идентификатор ресурсов.

| Идентификатор | Описание | Пример
|:---|:---|:---|
| Ресурс | Включает данные для ресурса. | ресурс ("/подписка/xxxxxxxxx-xxxx-xxxxxxxxxxxxxxxxxxxx/ресурсыгруппы/миресурсы/провайдеры/microsoft.compute/virtualmachines/myvm") |
| Группа ресурсов или подписка | Включает данные для ресурса и всех ресурсов, которые он содержит.  | ресурс ("/подписка/xxxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxx/ресурсы/группа ресурсов/myresourcegroup) |


## <a name="notes"></a>Примечания

* Вы должны прочитать доступ к ресурсу.


## <a name="examples"></a>Примеры

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Дальнейшие действия

- Ознакомиться [с областью действия запросов и диапазоном времени можно в журнале Azure Monitor Analytics,](scope.md) чтобы узнать подробную информацию о сфере запросов.
- Ознакомьтесь с полной документацией по [языку запросов Kusto](/azure/kusto/query/).
