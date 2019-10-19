---
title: Управление оповещениями из System Center Operations Manager, Zabbix и Nagios в Azure Monitor
description: Управление оповещениями из System Center Operations Manager, Zabbix и Nagios в Azure Monitor
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 09/24/2018
ms.openlocfilehash: 0e774633542f9b7f8b7de9f8f187821bfbe6b9cf
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555623"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Управление оповещениями из System Center Operations Manager, Zabbix и Nagios в Azure Monitor

Теперь вы можете просматривать оповещения от Nagios, Zabbix и System Center Operations Manager в [Azure Monitor](https://aka.ms/azure-alerts-overview). Эти оповещения поступают в Log Analytics из интегрированных серверов Nagios и Zabbix либо System Center Operations Manager. 

## <a name="prerequisites"></a>Технические условия
Любые записи в репозитории Log Analytics с типом оповещения будут импортироваться в Azure Monitor, поэтому вам нужно выполнить настройку, которая требуется для сбора этих записей.
1. Для оповещений **Nagios** и **Zabbix** настройте [соответствующие серверы](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) на [отправку оповещений](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) в Log Analytics.
1. Для оповещений **System Center Operations Manager** [подключите группу управления Operations Manager к рабочей области Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). После этого разверните [решение "Управление оповещениями" в Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) из решений Azure Marketplace. После этого все оповещения, созданные в System Center Operations Manager, импортируются в Log Analytics.

## <a name="view-your-alert-instances"></a>Просмотр экземпляров оповещений
Настроив импорт в Log Analytics, вы можете просматривать экземпляры оповещений от этих служб мониторинга в [Azure Monitor](https://aka.ms/azure-alerts-overview). Когда эти оповещения появятся в Azure Monitor, вы сможете [управлять экземплярами оповещений](https://aka.ms/managing-alert-instances), [управлять смарт-группами, созданными на основе этих оповещений,](https://aka.ms/managing-smart-groups) и [изменять состояние оповещений и смарт-групп](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Это решение позволяет только просматривать System Center Operations Manager/Zabbix/Nagios, которые активировали экземпляры предупреждений в Azure Monitor. Вы можете просмотреть или изменить конфигурацию правила генерации оповещений только в соответствующих средствах мониторинга. 
>  1. Все сработавшие экземпляры оповещений будут доступны как в Azure Monitor, так и в Azure Log Analytics. Сейчас нет возможности выбирать между ними или получать только определенные сработавшие оповещения.
>  1. Все оповещения от System Center Operations Manager, Zabbix и Nagios имеют тип сигнала "Unknown", так как базовый тип телеметрии недоступен.
>  1. Оповещения Nagios не поддерживают отслеживание состояния. Например, [состояние монитора](https://aka.ms/azure-alerts-overview) оповещения не изменится со "Сработало" на "Разрешено". Вместо этого оповещения с такими состояниями отображаются как отдельные экземпляры оповещений. 

