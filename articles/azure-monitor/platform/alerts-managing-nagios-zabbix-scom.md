---
title: Управление оповещениями из System Center Operations Manager, Zabbix и Nagios в Azure Monitor
description: Управление оповещениями из System Center Operations Manager, Zabbix и Nagios в Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 367a7020e56db7105da7624c7911ae5b59f1365a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105895"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Управление оповещениями из System Center Operations Manager, Zabbix и Nagios в Azure Monitor

Теперь вы можете просматривать оповещения от Nagios, Zabbix и System Center Operations Manager в [Azure Monitor](./alerts-overview.md). Эти оповещения поступают в Log Analytics из интегрированных серверов Nagios и Zabbix либо System Center Operations Manager. 

## <a name="prerequisites"></a>Предварительные требования
Любые записи в репозитории Log Analytics с типом оповещения будут импортироваться в Azure Monitor, поэтому вам нужно выполнить настройку, которая требуется для сбора этих записей.
1. Для оповещений **Nagios** и **Zabbix** [Настройте эти серверы](../learn/quick-collect-linux-computer.md) для [отправки оповещений](./data-sources-custom-logs.md?toc=/azure/azure-monitor/toc.json) в log Analytics.
1. Для **System Center Operations Manager** оповещений [подключите группу управления Operations Manager к рабочей области log Analytics](./om-agents.md). После этого разверните [решение "Управление оповещениями" в Azure Log Analytics](./alert-management-solution.md) из решений Azure Marketplace. После этого все оповещения, созданные в System Center Operations Manager, импортируются в Log Analytics.

## <a name="view-your-alert-instances"></a>Просмотр экземпляров оповещений
Настроив импорт в Log Analytics, вы можете просматривать экземпляры оповещений от этих служб мониторинга в [Azure Monitor](./alerts-overview.md). Когда эти оповещения появятся в Azure Monitor, вы сможете [управлять экземплярами оповещений](./alerts-managing-alert-instances.md?toc=%252fazure%252fazure-monitor%252ftoc.json), [управлять смарт-группами, созданными на основе этих оповещений,](./alerts-managing-smart-groups.md?toc=%252fazure%252fazure-monitor%252ftoc.json) и [изменять состояние оповещений и смарт-групп](./alerts-managing-alert-states.md?toc=%252fazure%252fazure-monitor%252ftoc.json).

> [!NOTE]
>  1. Это решение позволяет только просматривать System Center Operations Manager/Zabbix/Nagios, которые активировали экземпляры предупреждений в Azure Monitor. Вы можете просмотреть или изменить конфигурацию правила генерации оповещений только в соответствующих средствах мониторинга. 
>  1. Все сработавшие экземпляры оповещений будут доступны как в Azure Monitor, так и в Azure Log Analytics. Сейчас нет возможности выбирать между ними или получать только определенные сработавшие оповещения.
>  1. Все оповещения от System Center Operations Manager, Zabbix и Nagios имеют тип сигнала "Unknown", так как базовый тип телеметрии недоступен.
>  1. Оповещения Nagios не поддерживают отслеживание состояния. Например, [состояние монитора](./alerts-overview.md) оповещения не изменится со "Сработало" на "Разрешено". Вместо этого оповещения с такими состояниями отображаются как отдельные экземпляры оповещений.