---
title: Информация о сборе данных для решений по управлению в Azure | Документация Майкрософт
description: Решения по управлению в Azure — это собой коллекция правил логики, визуализации и получения данных, которые предоставляют метрики, связанные с определенной проблемной областью.  Эта статья содержит список решений по управлению, предлагаемых корпорацией Майкрософт, и сведения о методах и частоте сбора данных в этих решениях.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 6da2b63b7a9c8dc490bd30bb02ae6e7d932b2f7b
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683925"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Информация о сборе данных для решений по управлению в Azure
Эта статья содержит список [решений по управлению](solutions.md), предлагаемых корпорацией Майкрософт, со ссылками на подробную документацию по ним.  Здесь вы также найдете сведения о методах и частоте сбора данных в Azure Monitor.  Сведения в этой статье помогут вам оценить доступные решения и изучить потоки данных и требования к подключению для разных решений по управлению. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-management-solutions"></a>Список решений по управлению

В следующей таблице перечислены [решения по управлению](solutions.md), предоставленных корпорацией Майкрософт для Azure. Отметка в столбце означает, что конкретное решение использует соответствующий метод для сбора данных в Azure Monitor.  Те решения, для которых не отмечен ни один столбец, записывают данные в Azure Monitor напрямую из другой службы Azure. Ссылки, предложенные для каждого из решений, ведут на подробную документацию, где вы можете получить дополнительную информацию.

Ниже перечислены столбцы этой таблицы с пояснениями.

- **Microsoft Monitoring Agent** используется в Windows и Linux для запуска пакета управления из SCOM и решений по управлению из Azure. В этой конфигурации агент подключается напрямую к Azure Monitor, не используя группу управления Operations Manager. 
- **Operations Manager** — это точно такой же агент, как Microsoft Monitoring Agent. Но в этой конфигурации он [подключается к группе управления Operations Manager](../../azure-monitor/platform/om-agents.md), которая, в свою очередь, подключена к Azure Monitor. 
-  **Хранилище Azure** означает, что решение собирает данные через учетную запись хранения Azure. 
- **Нужен ли Operations Manager?** Подключение к группе управления Operations Manager является обязательным условием для сбора данных с помощью этого решения по управлению. 
- **Данные агента Operations Manager отправляются через группу управления**. Если агент [подключен к группе управления SCOM](../../azure-monitor/platform/om-agents.md), то он отправляет данные в Azure Monitor с сервера управления. В этой конфигурации агенту не требуется прямое подключение к службе Azure Monitor. Если в этом поле нет отметки, все данные отправляются от агента напрямую в Azure Monitor даже в том случае, если агент подключен к группе управления SCOM. Это означает, что ему необходима возможность взаимодействовать с Log Analytics с помощью [шлюза Azure Monitor](../../azure-monitor/platform/gateway.md).
- **Частота сбора** обозначает частоту сбора данных в этом решении по управлению. 



| **Решение по управлению** | **Платформа** | **Microsoft Monitoring Agent** | **Агент Operations Manager** | **Служба хранилища Azure** | **Нужен ли Operations Manager?** | **Данные агента Operations Manager отправляются через группу управления** | **Частота сбора** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Аналитический журнал действий](../../azure-monitor/platform/collect-activity-logs.md) | Таблицы Azure | | | | | | при уведомлении |
| [Оценка AD](../../azure-monitor/insights/ad-assessment.md) | Windows |&#8226; |&#8226; | | |&#8226; |7 дней |
| [Состояние репликации AD](../../azure-monitor/insights/ad-replication-status.md) | Windows |&#8226; |&#8226; | | |&#8226; |5 дней |
| [Работоспособность агентов](solution-agenthealth.md) | Windows и Linux | &#8226; | &#8226; | | | &#8226; | 1 минута |
| [Управление оповещениями](../../azure-monitor/platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |При получении |
| [Управление оповещениями](../../azure-monitor/platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 минута |
| [Управление оповещениями](../../azure-monitor/platform/alert-management-solution.md) (Operations Manager) | Windows | |&#8226; | |&#8226; |&#8226; |3 минуты |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Таблицы Azure | | | | | | Недоступно |
| [Соединитель Application Insights (не рекомендуется)](../../azure-monitor/platform/app-insights-connector.md) | Таблицы Azure | | | |  |  | при уведомлении |
| [Гибридная рабочая роль службы автоматизации](../../automation/automation-hybrid-runbook-worker.md) |  Windows | &#8226; | &#8226; |  |  |  | Недоступно |
| [Анализ шлюзов приложений Azure](../../azure-monitor/insights/azure-networking-analytics.md) | Таблицы Azure |  |  |  |  |  | при уведомлении |
| **Решение по управлению** | **Платформа** | **Microsoft Monitoring Agent** | **Агент Operations Manager** | **Служба хранилища Azure** | **Нужен ли Operations Manager?** | **Данные агента Operations Manager отправляются через группу управления** | **Частота сбора** |
| [Аналитика групп безопасности сетей Azure (не рекомендуется)](../../azure-monitor/insights/azure-networking-analytics.md) | Таблицы Azure |  |  |  |  |  | при уведомлении |
| [Службы анализа SQL Azure (предварительная версия)](../../azure-monitor/insights/azure-sql.md) |  Windows | | | | | | 1 минута |
| [Архивация](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Таблицы Azure |  |  |  |  |  | при уведомлении |
| [Емкость и производительность (предварительная версия)](../../azure-monitor/insights/capacity-performance.md) | Windows |&#8226; |&#8226; | | |&#8226; |При получении |
| [Отслеживание изменений](../../automation/automation-change-tracking.md) | Windows |&#8226; |&#8226; | | |&#8226; |[зависит от](../../automation/automation-change-tracking.md#change-tracking-data-collection-details) |
| [Отслеживание изменений](../../automation/automation-change-tracking.md) |Linux |&#8226; | | | | |[зависит от](../../automation/automation-change-tracking.md#change-tracking-data-collection-details) |
| [Контейнеры](../../azure-monitor/insights/containers.md) | Windows и Linux | &#8226; | &#8226; |  |  |  | 3 минуты |
| [Анализ хранилища ключей](../../azure-monitor/insights/azure-key-vault.md) | Windows | | | | | |при уведомлении |
| [Оценка вредоносных программ](../../security-center/security-center-install-endpoint-protection.md) | Windows |&#8226; |&#8226; | | |&#8226; |Ежечасно |
| [Монитор производительности сети](../../azure-monitor/insights/network-performance-monitor.md) |  Windows | &#8226; | &#8226; |  |  |  | Подтверждения TCP выполняются каждые 5 секунд, данные отправляются каждые 3 минуты |
| [Аналитика Office 365 (предварительная версия)](solution-office-365.md) | Windows | | | | | |при уведомлении |
| **Решение по управлению** | **Платформа** | **Microsoft Monitoring Agent** | **Агент Operations Manager** | **Служба хранилища Azure** | **Нужен ли Operations Manager?** | **Данные агента Operations Manager отправляются через группу управления** | **Частота сбора** |
| [Анализ Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) | Windows | | |&#8226; | | |5 мин |
| [Схема услуги](../../azure-monitor/insights/service-map.md) | Windows и Linux | &#8226; | &#8226; |  |  |  | 15 секунд |
| [Оценка SQL](../../azure-monitor/insights/sql-assessment.md) | Windows |&#8226; |&#8226; | | |&#8226; |7 дней |
| [SurfaceHub](../../azure-monitor/insights/surface-hubs.md) | Windows |&#8226; | | | | |При получении |
| [Оценка System Center Operations Manager (предварительная версия)](../../azure-monitor/insights/scom-assessment.md) |  Windows | &#8226; | &#8226; |  |  | &#8226; | 7 дней |
| [Управление обновлениями](../../automation/automation-update-management.md) |  Windows |&#8226; |&#8226; | | |&#8226; |не меньше 2 раз в день и через 15 минут после установки обновления |
| [Готовность к обновлению](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) |  Windows | &#8226; |  |  |  |  | 2 дня |
| [Мониторинг VMware (не рекомендуется)](../../azure-monitor/insights/vmware.md) | Linux | &#8226; |  |  |  |  | 3 минуты |
| [Wire Data 2.0 (предварительная версия)](../../azure-monitor/insights/wire-data.md) |Windows (2012 R2/8.1 или более поздней версии) |&#8226; |&#8226; | | | | 1 минута |




## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [создавать запросы](../../azure-monitor/log-query/log-query-overview.md) для анализа данных, собранных решением по управлению.
