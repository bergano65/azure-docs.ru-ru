---
title: Мониторинг контейнеров с журналами Azure Monitor
description: Используйте журналы Azure Monitor для мониторинга контейнеров, работающих в кластерах Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d4231de13da3f8b2960bd4852136f803a97a546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614440"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Мониторинг контейнеров с журналами Azure Monitor
 
В этой статье рассматриваются шаги, необходимые для настройки решения мониторинга контейнеров журналов Azure Monitor для просмотра событий контейнеров. См. дополнительные сведения о [настройке кластера для сбора событий контейнера](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Настройка решения для мониторинга контейнера

> [!NOTE]
> Для кластера необходимо настроить журналы Azure Monitor, а также развернуть агент универсала журналов журнала на узлах. Если вы этого не сможете, выполните последующие действия в [журналах Setup Monitor](service-fabric-diagnostics-oms-setup.md) и [сначала добавьте в кластер агент analytics журнала.](service-fabric-diagnostics-oms-agent.md)

1. После настройки кластера с журналами Azure Monitor и агентом log Analytics разместите контейнеры. Прежде чем перейти к следующему шагу, подождите, пока ваши контейнеры развернутся.

2. Найдите *Решение для мониторинга контейнеров* в Azure Marketplace и щелкните ресурс **Решения для мониторинга контейнеров** в категории "Мониторинг и управление".

    ![Добавление решения "Контейнеры"](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Создайте решение внутри рабочей области, созданной для кластера. При таком изменении агент автоматически активируется для запуска сбора данных Docker в контейнеры. Приблизительно через 15 минут в решении появятся входящие журналы и статистика, как показано на рисунке ниже.

    ![Базовая панель мониторинга Log Analytics](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Агент позволяет собирать несколько журналов, специфичным для контейнеров, которые могут быть запрошены в журналах Azure Monitor, или использоваться для визуализации показателей производительности. Собираются следующие типы журналов.

* ContainerInventory — отображает информацию о местонахождении контейнера, имени и образах.
* ContainerImageInventory — информация о развернутых образах, в том числе их идентификаторы или размеры.
* ContainerLog — журналы конкретных ошибок, журналы Docker (StdOut и т. д.) и другие записи.
* ContainerServiceLog — выполнявшиеся команды управляющей программы Docker.
* Perf — счетчики производительности контейнера, в том числе ЦП, память, сетевой трафик, операции ввода и вывода на диске и пользовательские метрики с компьютеров узла.



## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше об [решении журналов Журналов Azure Monitor.](../azure-monitor/insights/containers.md)
* Дополнительные сведения об оркестрации контейнера в Service Fabric см. в статье [Service Fabric и контейнеры](service-fabric-containers-overview.md)
* Ознакомьтесь с функциями [поиска журналов и запросов,](../log-analytics/log-analytics-log-searches.md) предлагаемых в рамках журналов Azure Monitor
* Настройка журналов Azure Monitor для [настройки автоматизированных](../log-analytics/log-analytics-alerts.md) правил оповещения для оказания помощи в обнаружении и диагностике