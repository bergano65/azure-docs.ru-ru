---
title: Мониторинг контейнеров в Service Fabric с помощью журналов Azure Monitor | Документация Майкрософт
description: Используйте журналы Azure Monitor для мониторинга контейнеров, работающих в кластерах Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d03d68560502821b9c343be983d9f7b5a83ed977
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60321918"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Мониторинг контейнеров с помощью журналов Azure Monitor
 
В этой статье рассматриваются шаги, необходимые для настройки решения мониторинга контейнеров журналы Azure Monitor для просмотра событий контейнера. См. дополнительные сведения о [настройке кластера для сбора событий контейнера](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Настройка решения для мониторинга контейнера

> [!NOTE]
> Необходимо иметь Azure Monitor журналы для кластера, а также развернуть на узлах агент Log Analytics. Если не сделать, выполните действия, описанные в [настроить журналы Azure Monitor](service-fabric-diagnostics-oms-setup.md) и [Добавление агента Log Analytics в кластер](service-fabric-diagnostics-oms-agent.md) первого.

1. После настройки кластера с журналами Azure Monitor и агент Log Analytics, разверните контейнеры. Прежде чем перейти к следующему шагу, подождите, пока ваши контейнеры развернутся.

2. Найдите *Решение для мониторинга контейнеров* в Azure Marketplace и щелкните ресурс **Решения для мониторинга контейнеров** в категории "Мониторинг и управление".

    ![Добавление решения "Контейнеры"](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Создайте решение внутри рабочей области, созданной для кластера. При таком изменении агент автоматически активируется для запуска сбора данных Docker в контейнеры. Приблизительно через 15 минут в решении появятся входящие журналы и статистика, как показано на рисунке ниже.

    ![Базовая панель мониторинга Log Analytics](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Агент включает сбор нескольких журналов конкретного контейнера, которые можно запрашивать в журналах Azure Monitor, и использовать для визуализации показателей эффективности. Собираются следующие типы журналов.

* ContainerInventory — отображает информацию о местонахождении контейнера, имени и образах.
* ContainerImageInventory — информация о развернутых образах, в том числе их идентификаторы или размеры.
* ContainerLog — журналы конкретных ошибок, журналы Docker (StdOut и т. д.) и другие записи.
* ContainerServiceLog — выполнявшиеся команды управляющей программы Docker.
* Perf — счетчики производительности контейнера, в том числе ЦП, память, сетевой трафик, операции ввода и вывода на диске и пользовательские метрики с компьютеров узла.



## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о [Azure Monitor входит решение "контейнеры"](../azure-monitor/insights/containers.md).
* Дополнительные сведения об оркестрации контейнера в Service Fabric см. в статье [Service Fabric и контейнеры](service-fabric-containers-overview.md)
* Ознакомьтесь с функциями [поиска и запроса журналов](../log-analytics/log-analytics-log-searches.md) функции, предоставляемые как часть журналов Azure Monitor
* Настройка журналов Azure Monitor для настройки [автоматические оповещения](../log-analytics/log-analytics-alerts.md) правила помогают выполнять обнаружение и диагностика