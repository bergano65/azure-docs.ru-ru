---
title: Настройка и чтение журналов при использовании активации функций Azure для Cosmos DB
description: Узнайте, как предоставить журналы в конвейер ведения журнала "Функции Azure" при использовании активации функций Azure для Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 17b2d4854e3222e5bdb607c6c80b0ec5ed6177d3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093603"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Процесс настройки и чтения журналов при использовании активации функций Azure для Cosmos DB

В этой статье показано, как можно настроить среду "Функции Azure", чтобы отправить активацию функций Azure для журналов Azure Cosmos DB в настроенное [Monitor Azure Functions](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Доступные журналы

При активации функций Azure для Cosmos DB для используется [библиотека обработчика канала изменений](./change-feed-processor.md) на внутреннем уровне. Библиотека создает набор журналов работоспособности, которые могут использоваться для мониторинга внутренних операций в целях [устранения неполадок](./troubleshoot-changefeed-functions.md).

В журналах работоспособности описывается, как ведет себя активация функций Azure для Cosmos DB при выполнении операций в сценариях балансировки нагрузки или инициализации.

## <a name="enabling-logging"></a>Включение ведения журналов

Чтобы включить ведение журналов при использовании активации функций Azure для Cosmos DB, найдите файл `host.json` в проекте Функций Azure или приложении Функций Azure и [настройте требуемый уровень ведения журнала](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Необходимо включить данные трассировки `Host.Triggers.CosmosDB`, как показано в следующем примере:

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

Когда будут развернуты Функции Azure с обновленной конфигурацией, журналы активации функций Azure для Cosmos DB отобразятся как часть трассировки. Журналы можно просмотреть в настроенном поставщике ведения журнала в разделе *Категории* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Выполнение запросов к журналам

Выполните следующий запрос к журналам, созданным с помощью активации функций Azure для Cosmos DB в [Overview of log queries in Azure Monitor](../azure-monitor/app/analytics.md) (Обзор запросов журнала в Azure Monitor):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Следующие шаги

* [Включение мониторинга](../azure-functions/functions-monitoring.md) в приложениях Функций Azure.
* Узнайте, как выполнять [диагностику и устранять неполадки при использовании активации функций Azure для Cosmos DB](./troubleshoot-changefeed-functions.md).