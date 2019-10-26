---
title: выражение Resource () в Azure Monitor запросе журнала | Документация Майкрософт
description: Выражение ресурса используется в Azure Monitor запросе к журналу на основе ресурсов для получения данных из нескольких ресурсов.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 4787dc8a8cc06e0ad0be88597dab3481284fb58b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900215"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>выражение Resource () в Azure Monitor запросе журнала

`resource` выражение используется в Azure Monitorном запросе к [ресурсу](scope.md#query-scope) для получения данных из других ресурсов. 


## <a name="syntax"></a>Синтаксис

`resource(`*Идентификатор*`)`

## <a name="arguments"></a>Аргументы

- *Идентификатор*: идентификатор ресурса.

| Идентификатор | Описание | Пример
|:---|:---|:---|
| Ресурс | Включает данные для ресурса. | ресурс ("/субскриптионс/кскскскскскскс-кскскскс-кскскскс-кскскскс-кскскскскскскскскскскскс/ресаурцесграупс/миресаурцеграуп/провидерс/Микрософт.компуте/виртуалмачинес/мивм") |
| Группа ресурсов или подписка | Включает данные для ресурса и всех содержащихся в нем ресурсов.  | ресурс ("/субскриптионс/кскскскскскскс-кскскскс-кскскскс-кскскскс-кскскскскскскскскскскскс/ресаурцесграупс/миресаурцеграуп") |


## <a name="notes"></a>Заметки

* Необходимо иметь доступ на чтение к ресурсу.


## <a name="examples"></a>Примеры

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о области запроса см. [в разделе Область запроса журнала и временной диапазон в Azure Monitor log Analytics](scope.md) .
- Ознакомьтесь с полной документацией по [языку запросов Kusto](/azure/kusto/query/).
