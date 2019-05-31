---
title: Обзор аналитики в Azure Monitor | Документация Майкрософт
description: Insights обеспечить настраиваемую среду мониторинга в Azure Monitor для отдельных приложений и служб. Статья содержит краткое описание каждого из аналитическими данными, которые в настоящее время доступны.
services: azure-monitor
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: bwren
ms.openlocfilehash: 8cb39a174c570b7019e872d731f49252a9505406
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247234"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Обзор аналитики в Azure Monitor
Insights обеспечить настраиваемую среду наблюдения для отдельных приложений и служб. Они хранят данные в [платформы данных Azure Monitor](../platform/data-platform.md) и использовать другие компоненты Azure Monitor для анализа и создания оповещений, но может собирать дополнительные данные и работы уникальных пользователей на портале Azure. Доступ к информации из **Insights** меню Azure Monitor на портале Azure.

Следующие разделы содержат краткое описание аналитическими данными, которые в настоящее время доступны в Azure Monitor. См. в разделе подробную документацию подробности на каждом.

## <a name="application-insights"></a>Application Insights
Application Insights — это расширяемая служба управления производительностью приложений (APM) для веб-разработчиков на нескольких платформах. Используйте ее для мониторинга вашего работающего веб-приложения. Он работает для приложений на разнообразных платформах, включая .NET, Node.js и Java EE, размещенных в локальной, гибридной или любого общедоступного облака. Он также интегрируется с процессом DevOps и содержит точки подключения ко многим инструментам разработки.

См. в разделе [что такое Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor для контейнеров
Azure Monitor для контейнеров отслеживает производительность рабочих нагрузок контейнеров, развернутых в службе "Экземпляры контейнеров Azure" или на управляемых кластерах Kubernetes в Службе Azure Kubernetes (AKS). Мониторинг контейнеров крайне важен, особенно если вы управляете рабочим кластером в нужном масштабе с несколькими приложениями.

См. в разделе [Azure Monitor для Общие сведения о контейнерах](../insights/container-insights-overview.md).

![Azure Monitor для контейнеров](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor для групп ресурсов (Предварительная версия)
Azure Monitor для групп ресурсов позволяет рассматривать и диагностировать любые проблем отдельных ресурсов, предоставляющее контекст относительно работоспособность и производительность группы ресурсов как единое целое.

См. в разделе [мониторинг групп ресурсов с помощью Azure Monitor (Предварительная версия)](../insights/resource-group-insights.md).

![Azure Monitor для групп ресурсов](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor для виртуальных машин (Предварительная версия)
Azure Monitor для виртуальных машин отслеживает виртуальные машины Azure и масштабируемые наборы виртуальных машин в нужном масштабе. Это решение анализирует производительность и работоспособность виртуальных машин Windows и Linux, отслеживает их процессы и зависимости на других ресурсах, а также внешние процессы.

См. в разделе [что такое Azure Monitor для виртуальных машин?](vminsights-overview.md)

![Azure Monitor для виртуальных машин](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о [платформы данных Azure Monitor](../platform/data-platform.md) оптимизированный за счет использования аналитики.
* Дополнительные сведения о различных [источников данных, используемых с Azure Monitor](../platform/data-sources.md) и различные виды данных, собранных каждым аналитики.
