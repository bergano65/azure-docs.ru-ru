---
title: Настройка и чтение журналов с помощью триггеров функций Azure для Cosmos DB
description: Узнайте, как предоставить журналы в конвейер ведения журнала "Функции Azure" при использовании активации функций Azure для Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 5ff747b225f8984bcaafd80015e85a9f014bdb50
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441830"
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

Когда будут развернуты Функции Azure с обновленной конфигурацией, журналы активации функций Azure для Cosmos DB отобразятся как часть трассировки. Вы можете просмотреть журналы в настроенном поставщике ведения журнала в *категории* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Выполнение запросов к журналам

Выполните следующий запрос к журналам, созданным с помощью активации функций Azure для Cosmos DB в [Overview of log queries in Azure Monitor](../azure-monitor/app/analytics.md) (Обзор запросов журнала в Azure Monitor):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Дальнейшие действия

* [Включение мониторинга](../azure-functions/functions-monitoring.md) в приложениях Функций Azure.
* Узнайте, как выполнять [диагностику и устранять неполадки при использовании активации функций Azure для Cosmos DB](./troubleshoot-changefeed-functions.md).