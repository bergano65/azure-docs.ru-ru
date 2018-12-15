---
title: Ребрендинг Azure Monitor | Документация Майкрософт
description: Описание последних изменений имени и фирменной символики, недавно внесенных в службы управления Azure.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: bwren
ms.openlocfilehash: 68986b3d60566ee190a92c6ccf8cda767824350f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000151"
---
# <a name="azure-monitor-branding-changes"></a>Изменения фирменной символики Azure Monitor
В этой статье описываются последние изменения имени и фирменной символики, недавно внесенные в службы управления Azure. 

## <a name="consolidation-of-monitoring-services-into-azure-monitor"></a>Консолидация служб мониторинга в Azure Monitor
Log Analytics и Application Insights были объединены в Azure Monitor, чтобы предоставить единую интегрированную среду для мониторинга ресурсов Azure и гибридных сред. Никакие функциональные возможности не были удалены из этих служб, и пользователи могут выполнять те же сценарии, которые всегда выполняли, без потери или компрометации каких-либо возможностей.

Документация для каждой из этих служб была объединена в единый набор содержимого для Azure Monitor. Это поможет читателю в поиске содержимого для конкретного сценария мониторинга в одном месте, в отличие от необходимости ссылаться на несколько наборов контента. По мере развития консолидированной службы содержимое станет более интегрированным.

## <a name="log-analytics-redefinition"></a>Переопределение Log Analytics
Служба Log Analytics играет ключевую роль в управлении Azure. Она собирает данные телеметрии и другие данные из разнообразных источников и предоставляет язык запросов и механизм аналитики, который позволяет анализировать работу приложений и ресурсов. Она будет продолжать выполнять эту важную роль в качестве компонента Azure Monitor. Другие возможности, которые считались частью службы Log Analytics, такие как агенты и решения, будут размещены как компоненты Azure Monitor. Их функциональность осталась прежней. Единственное изменение — потенциальные улучшения интерфейса на портале Azure.

## <a name="retirement-of-operations-management-suite-brand"></a>Снятие с эксплуатации торговой марки Operations Management Suite
В целях лицензирования Operations Management Suite (OMS) объединял в себе следующие службы управления Azure.

- Application Insights
- Служба автоматизации Azure
- Служба архивации Azure
- Log Analytics
- Site Recovery

[Для этих служб были введены новые цены](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), и объединение OMS больше не доступно для новых клиентов. Ни одна из служб, которая была элементом OMS, не изменилась, за исключением консолидации в Azure Monitor, описанной выше. 



## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с [обзором Azure Monitor](overview.md), в котором описаны его различные компоненты и функции.
- Дополнительные сведения о [перемещении портала OMS](../azure-monitor/platform/oms-portal-transition.md).