---
title: Пример сценария Azure PowerShell для открытия порта приложения в подсистеме балансировки нагрузки | Документация Майкрософт
description: Пример сценария Azure PowerShell — открытие порта в Azure Load Balancer для приложения Service Fabric.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 05/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 72badb5ca2e8973fd64f5339eaff55ceb79a5ce3
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498270"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Открытие порта приложения в Azure Load Balancer

Приложения Service Fabric, работающие в Azure, находятся за Azure Load Balancer. Этот пример сценария открывает порт в Azure Load Balancer, чтобы приложение Service Fabric могли взаимодействовать с внешними клиентами. Измените параметры, если это необходимо. Если кластер находится в группе безопасности сети, также [добавьте правило входящего трафика для группы безопасности сети](service-fabric-powershell-add-nsg-rule.md), чтобы разрешить входящий трафик.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

При необходимости установите модуль PowerShell ServiceFabric вместе с [пакетом SDK для Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Получает ресурс Azure.  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | Получает Azure Load Balancer. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | Добавляет конфигурацию пробы для подсистемы балансировки нагрузки.|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | Получает конфигурацию пробы для подсистемы балансировки нагрузки. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | Добавляет правило конфигурации в подсистему балансировки нагрузки. |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | Задает состояние цели для подсистемы балансировки нагрузки. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).
