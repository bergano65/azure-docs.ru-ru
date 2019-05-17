---
title: Журналы триггеров Azure Cosmos DB
description: Узнайте, как предоставить журналы триггеров Azure Cosmos DB в конвейер ведения журналов Функций Azure
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 5/2/2019
ms.author: maquaran
ms.openlocfilehash: a598842ecde9508a6c2185a6097f689252fda60c
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515022"
---
# <a name="how-to-configure-and-read-the-azure-cosmos-db-trigger-logs"></a>Как настроить и просматривать журналы триггеров Azure Cosmos DB

В этой статье показано, как можно настроить среду Функций Azure для отправки журналов триггеров Azure Cosmos DB в настроенное [решение для мониторинга](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Доступные журналы

Триггер Azure Cosmos DB использует [библиотеку обработчика канала изменений](./change-feed-processor.md) на внутреннем уровне, и библиотека создает набор журналов работоспособности, которые могут использоваться для мониторинга внутренних операций в целях [устранения неполадок](./troubleshoot-changefeed-functions.md).

В журналах работоспособности описывается, как ведет себя триггер Azure Cosmos DB при выполнении операций в сценариях балансировки нагрузки или инициализации.

## <a name="enabling-logging"></a>Включение ведения журналов

Чтобы включить ведение журналов триггеров Azure Cosmos DB, найдите файл `host.json` в проекте Функций Azure или приложении Функций Azure и [настройте требуемый уровень ведения журнала](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Необходимо включить данные трассировки `Host.Triggers.CosmosDB`, как показано в следующем примере:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Когда будут развернуты Функции Azure с обновленной конфигурацией, журналы триггеров Azure Cosmos DB отобразятся как часть трассировки. Журналы можно просмотреть в настроенном поставщике ведения журнала в разделе *Категории* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Выполнение запросов к журналам

Выполните следующий запрос к журналам, созданным триггером Azure Cosmos DB в [Analytics в Application Insights ](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Дополнительная информация

* [Включение мониторинга](../azure-functions/functions-monitoring.md) в приложениях Функций Azure.
* Узнайте, как [выполнять диагностику и устранять распространенные неполадки](./troubleshoot-changefeed-functions.md) при использовании триггера Azure Cosmos DB в Функциях Azure.