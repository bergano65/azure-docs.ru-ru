---
title: Общие сведения о диагностике конфигурации сети в наблюдателе за сетями Azure | Документация Майкрософт
description: На этой странице представлен обзор наблюдателя за сетями — Диагностика конфигурации сети.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 4d308b8a1a589308e2481c8bb9e0dc9ec64be25b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996695"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Общие сведения о диагностике конфигурации сети в наблюдателе за сетями Azure

Средство диагностики конфигурации сети помогает клиентам понять, какие потоки трафика будут разрешены или запрещены в виртуальной сети Azure, а также подробные сведения для отладки. Она поможет вам понять, правильно ли настроены правила NSG. 

## <a name="pre-requisites"></a>Предварительные требования
Для использования диагностики сетевой конфигурации наблюдатель за сетями должен быть включен в подписке. См. раздел [Создание экземпляра наблюдателя за сетями Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-create) для включения.

## <a name="background"></a>История

- Ресурсы в Azure подключаются через виртуальные сети (виртуальных сетей) и подсети. Безопасность этих виртуальных сетей и подсетей можно управлять с помощью группы безопасности сети (NSG).
- NSG содержит список правил безопасности, которые разрешают или запрещают сетевой трафик к ресурсам, к которым он подключен. Группы безопасности сети можно связать с подсетями, отдельными виртуальными машинами или отдельными сетевыми интерфейсами (NIC), подключенными к виртуальным машинам. 
- Все потоки трафика в сети оцениваются с помощью правил в соответствующем NSG.
- Правила оцениваются на основе номера приоритета от нижнего к верхнему. 

## <a name="how-does-network-configuration-diagnostic-work"></a>Как работает диагностика сетевой конфигурации? 

Для данного потока средство НКД выполняет имитацию последовательности и возвращает сведения о том, разрешена ли последовательность (или запрещена), а также подробное описание правил, разрешающих или запрещающих последовательность.  Клиенты должны предоставить подробные сведения о потоке, например источник, назначение, протокол и т. д. Средство возвращает сведения о том, разрешен или запрещен трафик, правила NSG, которые были оценены для указанного потока, и результаты оценки для каждого правила.

## <a name="next-steps"></a>Дальнейшие шаги

Использование диагностики сетевой конфигурации с помощью других интерфейсов
 - [REST API](https://docs.microsoft.com/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic?view=azps-4.6.1)
 - [Azure CLI](https://docs.microsoft.com/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_run_configuration_diagnostic)

