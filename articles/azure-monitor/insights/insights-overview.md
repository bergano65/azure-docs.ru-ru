---
title: Обзор информации в Azure Monitor (ru) Документы Майкрософт
description: Исследования предоставляют индивидуальный опыт мониторинга в Azure Monitor для конкретных приложений и служб. В этой статье приводится краткое описание каждого из идей, которые в настоящее время доступны.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 15ea7698c9e90fa8b0dfa20f71b552a2b0e9c7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657254"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Обзор анализов в Azure Monitor
Исследования предоставляют индивидуальный опыт мониторинга для конкретных приложений и служб. Они хранят данные в [платформе данных Azure Monitor](../platform/data-platform.md) и используют другие функции Azure Monitor для анализа и оповещения, но могут собирать дополнительные данные и предоставлять уникальный пользовательский опыт на портале Azure. Получите доступ к сведениям из раздела **Insights** меню Azure Monitor на портале Azure.

В следующих разделах приводится краткое описание аналитических данных, которые в настоящее время доступны в Azure Monitor. Подробную информацию о каждом из них можно узнать из подробной документации.

## <a name="application-insights"></a>Application Insights
Application Insights — это расширяемая служба управления производительностью приложений (APM) для веб-разработчиков на нескольких платформах. Используйте ее для мониторинга вашего работающего веб-приложения. Он работает для приложений на самых разных платформах, включая .NET, Node.js и Java EE, размещенных на местах, гибридных или любых общедоступных облачных. Он также интегрируется с вашим процессом DevOps и имеет точки подключения к различным инструментам разработки.

Смотрите, [что такое приложение Исследования?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor для контейнеров
Azure Monitor для контейнеров отслеживает производительность рабочих нагрузок контейнеров, развернутых в службе "Экземпляры контейнеров Azure" или на управляемых кластерах Kubernetes в Службе Azure Kubernetes (AKS). Мониторинг контейнеров крайне важен, особенно если вы управляете рабочим кластером в нужном масштабе с несколькими приложениями.

Смотрите [обзор Azure Monitor для просмотра контейнеров.](../insights/container-insights-overview.md)

![Azure Monitor для контейнеров](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Монитор Azure для групп ресурсов (предварительный просмотр)
Azure Monitor для групп ресурсов помогает сортировать и диагностировать любые проблемы, с которыми сталкиваются ваши отдельные ресурсы, предлагая при этом контекст о работоспособности и производительности группы ресурсов в целом.

Смотрите [группы ресурсов Monitor с Azure Monitor (предварительный просмотр)](../insights/resource-group-insights.md).

![Монитор Azure для групп ресурсов](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor для vMs (предварительный просмотр)
Azure Monitor для виртуальных машин отслеживает виртуальные машины Azure и масштабируемые наборы виртуальных машин в нужном масштабе. Это решение анализирует производительность и работоспособность виртуальных машин Windows и Linux, отслеживает их процессы и зависимости на других ресурсах, а также внешние процессы.

Смотрите, [что такое Azure Monitor для vMs?](vminsights-overview.md)

![Azure Monitor для виртуальных машин](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Azure Monitor для сетей (предварительный просмотр)
[Azure Monitor for Networks](network-insights-overview.md) предоставляет полное представление о работоспособности и метриках для всего сетевого ресурса. Расширенная возможность поиска помогает определить зависимости ресурсов, позволяя такие сценарии, как определение ресурса, который размещает ваш веб-сайт, просто ища имя вашего сайта.

![Azure Monitor для сетей](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше о [платформе данных Azure Monitor,](../platform/data-platform.md) затрачиваемых на аналитические данные.
* Узнайте о различных [источниках данных, используемых Azure Monitor,](../platform/data-sources.md) и о различных видах данных, собранных каждым из данных.
