---
title: Общие сведения об аналитике в Azure Monitor | Документация Майкрософт
description: Службы аналитики предоставляют настраиваемый интерфейс в Azure Monitor для мониторинга конкретных приложений и служб. В этой статье содержится краткое описание каждого из доступных в настоящее время средств аналитики.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: e3eefb97c85b3865abbf116b9912dc45f6db8ce8
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326874"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Общие сведения об аналитике в Azure Monitor
Службы аналитики предоставляют настраиваемый интерфейс для мониторинга конкретных приложений и служб. Средства аналитики сохраняют данные на [платформе данных Azure Monitor](../platform/data-platform.md) и используют другие функции Azure Monitor для анализа и оповещений, а также могут собирать дополнительные данные и создавать уникальный интерфейс взаимодействия на портале Azure. Эти средства доступны в разделе **Аналитика** в меню Azure Monitor на портале Azure.


В следующих разделах содержится краткое описание каждого из доступных в настоящее время средств аналитики в Azure Monitor. Подробные сведения о каждом из них приводятся в документации.

## <a name="application-insights"></a>Application Insights
Application Insights — это расширяемая служба управления производительностью приложений (APM) для веб-разработчиков на нескольких платформах. Используйте ее для мониторинга вашего работающего веб-приложения. Application Insights работает с приложениями на разных платформах, включая .NET, Node.js и Java EE, с локальным, гибридным размещением или в любом общедоступном облаке. Эта служба также интегрируется с процессом DevOps и содержит точки подключения ко многим инструментам разработки.

См. статью [Что такое Azure Application Insights?](../app/app-insights-overview.md)

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor для контейнеров
Azure Monitor для контейнеров отслеживает производительность рабочих нагрузок контейнеров, развернутых в службе "Экземпляры контейнеров Azure" или на управляемых кластерах Kubernetes в Службе Azure Kubernetes (AKS). Мониторинг контейнеров крайне важен, особенно если вы управляете рабочим кластером в нужном масштабе с несколькими приложениями.

См. статью [Обзор службы "Azure Monitor для контейнеров"](./container-insights-overview.md).

![Azure Monitor для контейнеров](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor для групп ресурсов (предварительная версия)
Azure Monitor для групп ресурсов упрощает рассмотрение и диагностику любых проблем, с которыми сталкиваются отдельные ресурсы, а также предоставляется контекст относительно работоспособности и производительности группы ресурсов в целом.

См. статью [Мониторинг групп ресурсов с помощью Azure Monitor (предварительная версия)](./resource-group-insights.md).

![Azure Monitor для групп ресурсов](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor для виртуальных машин (предварительная версия)
Azure Monitor для виртуальных машин отслеживает виртуальные машины Azure и масштабируемые наборы виртуальных машин в нужном масштабе. Это решение анализирует производительность и работоспособность виртуальных машин Windows и Linux, отслеживает их процессы и зависимости на других ресурсах, а также внешние процессы.

См. статью [Общие сведения об Azure Monitor для виртуальных машин](vminsights-overview.md)

![Azure Monitor для виртуальных машин](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Azure Monitor для сетей (предварительная версия)
[Azure Monitor для сетей](network-insights-overview.md) предоставляет подробное представление о работоспособности и метриках для всех сетевых ресурсов. Возможность расширенного поиска помогает выявлять зависимости ресурсов в таких сценариях, как идентификация ресурсов, на которых размещен веб-сайт, по имени этого веб-сайта.

![Azure Monitor для сетей](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше о [платформе данных Azure Monitor](../platform/data-platform.md), используемой средствами аналитики.
* Изучите сведения об [источниках данных, используемых Azure Monitor](../platform/data-sources.md) и узнайте, какие типы данных собираются каждым средством аналитики.

