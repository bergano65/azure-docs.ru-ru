---
title: Обзор аналитических сведений в Azure Monitor | Документация Майкрософт
description: Insights предоставляет настраиваемый опыт мониторинга в Azure Monitor для конкретных приложений и служб. В этой статье содержится краткое описание каждого из доступных в настоящее время аналитических сведений.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: d1b53267daa2d47baf9976727bbaf3fa56172432
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832091"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Обзор аналитических сведений в Azure Monitor
Insights предоставляет настраиваемый интерфейс мониторинга для конкретных приложений и служб. Они хранят данные на [платформе данных Azure Monitor](../platform/data-platform.md) и используют другие функции Azure Monitor для анализа и оповещений, но могут содействовать дополнительные данные и обеспечивают уникальную работу пользователей в портал Azure. Получите доступ к аналитике из раздела " **аналитика** " в меню Azure Monitor в портал Azure.

В следующих разделах приводится краткое описание аналитических сведений, доступных в данный момент в Azure Monitor. Подробные сведения о каждой из них см. в подробной документации.

## <a name="application-insights"></a>Application Insights
Application Insights — это расширяемая служба управления производительностью приложений (APM) для веб-разработчиков на нескольких платформах. Используйте ее для мониторинга вашего работающего веб-приложения. Она работает для приложений на различных платформах, включая .NET, Node. js и Java EE, размещенную локально, гибридную среду или любое общедоступное облако. Она также интегрируется с процессом DevOps и имеет точки подключения к различным средствам разработки.

См. раздел [что такое Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor для контейнеров
Azure Monitor для контейнеров отслеживает производительность рабочих нагрузок контейнеров, развернутых в службе "Экземпляры контейнеров Azure" или на управляемых кластерах Kubernetes в Службе Azure Kubernetes (AKS). Мониторинг контейнеров крайне важен, особенно если вы управляете рабочим кластером в нужном масштабе с несколькими приложениями.

См. раздел [Общие сведения о Azure Monitor для контейнеров](../insights/container-insights-overview.md).

![Azure Monitor для контейнеров](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor для групп ресурсов (Предварительная версия)
Azure Monitor для групп ресурсов помогает в рассмотрении и диагностике проблем, возникающих при работе отдельных ресурсов, и предоставляет контекст для работоспособности и производительности группы ресурсов в целом.

См. раздел [мониторинг групп ресурсов с помощью Azure Monitor (Предварительная версия)](../insights/resource-group-insights.md).

![Azure Monitor для групп ресурсов](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor для виртуальных машин (Предварительная версия)
Azure Monitor для виртуальных машин отслеживает виртуальные машины Azure и масштабируемые наборы виртуальных машин в нужном масштабе. Они анализируют производительность и работоспособность виртуальных машин Windows и Linux, отслеживают их процессы и зависимости на других ресурсах, а также внешние процессы.

Узнайте [, что такое Azure Monitor для виртуальных машин?](vminsights-overview.md)

![Azure Monitor для виртуальных машин](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Azure Monitor для сетей (Предварительная версия)
[Azure Monitor для сетей](network-insights-overview.md) предоставляет исчерпывающее представление о работоспособности и метриках для всех сетевых ресурсов. Функция расширенного поиска помогает определить зависимости ресурсов, включая такие сценарии, как идентификация ресурсов, на которых размещен ваш веб-сайт, путем простого поиска имени веб-сайта.

![Azure Monitor для сетей](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о [платформе данных Azure Monitor](../platform/data-platform.md) , используемой аналитикой.
* Узнайте о различных [источниках данных, используемых Azure Monitor](../platform/data-sources.md) , а также о различных типах данных, собираемых каждым из ценных сведений.
