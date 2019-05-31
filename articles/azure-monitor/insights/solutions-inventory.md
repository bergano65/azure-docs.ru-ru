---
title: Инвентаризация решений в Azure для мониторинга | Документация Майкрософт
description: Решения мониторинга в Azure Monitor — это коллекция правил логики, визуализации и получения данных, которые предоставляют метрики, связанные с определенной проблемной областью.  В этой статье список решений корпорации Майкрософт и сведения о методах и частоте сбора данных для мониторинга.
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
ms.openlocfilehash: 9398815ea75c0eacd99a6e40c569254fac671cbb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234021"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Сведения о сборе инвентаризации и данных для мониторинга решений в Azure
[Мониторинг решений](solutions.md) используют службы Azure, чтобы получить дополнительные сведения о работе конкретного приложения или службы. Как правило, решения мониторинга собирают данные журналов, а также предоставляют запросы и представления для анализа собранных данных. В Azure Monitor вы можете добавить решения мониторинга для всех используемых приложений и служб. Обычно они предоставляются бесплатно, но сбор данных может повлечь определенную плату за использование ресурсов.

Эта статья содержит список [montioring решения](solutions.md) корпорации Microsoft со ссылками на подробную документацию по ним.  Здесь вы также найдете сведения о методах и частоте сбора данных в Azure Monitor.  Сведения в этой статье можно использовать для идентификации доступные решения и понять требования потока и подключения данных для различных решений для мониторинга.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Список решений для мониторинга

В следующей таблице перечислены [решения для мониторинга](solutions.md) в Azure, предоставленных корпорацией Майкрософт. Отметка в столбце означает, что конкретное решение использует соответствующий метод для сбора данных в Azure Monitor.  Те решения, для которых не отмечен ни один столбец, записывают данные в Azure Monitor напрямую из другой службы Azure. Ссылки, предложенные для каждого из решений, ведут на подробную документацию, где вы можете получить дополнительную информацию.

Ниже перечислены столбцы этой таблицы с пояснениями.

- **Агент мониторинга Майкрософт** - агентов, используемых в Windows и Linux для запуска пакета управления из SCOM и решений из Azure для мониторинга. В этой конфигурации агент подключается напрямую к Azure Monitor, не используя группу управления Operations Manager. 
- **Operations Manager** — это точно такой же агент, как Microsoft Monitoring Agent. Но в этой конфигурации он [подключается к группе управления Operations Manager](../platform/om-agents.md), которая, в свою очередь, подключена к Azure Monitor. 
-  **Хранилище Azure** означает, что решение собирает данные через учетную запись хранения Azure. 
- **Нужен ли Operations Manager?** -Подключенной группы управления Operations Manager является обязательным для сбора данных, решением для мониторинга. 
- **Данные агента Operations Manager отправляются через группу управления**. Если агент [подключен к группе управления SCOM](../platform/om-agents.md), то он отправляет данные в Azure Monitor с сервера управления. В этой конфигурации агенту не требуется прямое подключение к службе Azure Monitor. Если в этом поле нет отметки, все данные отправляются от агента напрямую в Azure Monitor даже в том случае, если агент подключен к группе управления SCOM. Это означает, что ему необходима возможность взаимодействовать с Log Analytics с помощью [шлюза Azure Monitor](../platform/gateway.md).
- **Частота сбора** -указывает частоту сбора данных для мониторинга решения. 



| **Решение для мониторинга** | **Платформа** | **Microsoft Monitoring Agent** | **Агент Operations Manager** | **Служба хранилища Azure** | **Нужен ли Operations Manager?** | **Данные агента Operations Manager отправляются через группу управления** | **Частота сбора** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Аналитический журнал действий](../platform/activity-log-collect.md) | Таблицы Azure | | | | | | при уведомлении |
| [Оценка AD](ad-assessment.md) | Windows |&#8226; |&#8226; | | |&#8226; |7 дней |
| [Состояние репликации AD](ad-replication-status.md) | Windows |&#8226; |&#8226; | | |&#8226; |5 дней |
| [Работоспособность агентов](solution-agenthealth.md) | Windows и Linux | &#8226; | &#8226; | | | &#8226; | 1 минута |
| [Управление оповещениями](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |При получении |
| [Управление оповещениями](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 минута |
| [Управление оповещениями](../platform/alert-management-solution.md) (Operations Manager) | Windows | |&#8226; | |&#8226; |&#8226; |3 минуты |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Таблицы Azure | | | | | | Недоступно |
| [Соединитель Application Insights (не рекомендуется)](../platform/app-insights-connector.md) | Таблицы Azure | | | |  |  | при уведомлении |
| [Гибридная рабочая роль службы автоматизации](../../automation/automation-hybrid-runbook-worker.md) |  Windows | &#8226; | &#8226; |  |  |  | Недоступно |
| [Анализ шлюзов приложений Azure](azure-networking-analytics.md) | Таблицы Azure |  |  |  |  |  | при уведомлении |
| **Решение для мониторинга** | **Платформа** | **Microsoft Monitoring Agent** | **Агент Operations Manager** | **Служба хранилища Azure** | **Нужен ли Operations Manager?** | **Данные агента Operations Manager отправляются через группу управления** | **Частота сбора** |
| [Аналитика групп безопасности сетей Azure (не рекомендуется)](azure-networking-analytics.md) | Таблицы Azure |  |  |  |  |  | при уведомлении |
| [Службы анализа SQL Azure (предварительная версия)](azure-sql.md) |  Windows | | | | | | 1 минута |
| [Архивация](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Таблицы Azure |  |  |  |  |  | при уведомлении |
| [Емкость и производительность (предварительная версия)](capacity-performance.md) | Windows |&#8226; |&#8226; | | |&#8226; |При получении |
| [Отслеживание изменений](../../automation/change-tracking.md) | Windows |&#8226; |&#8226; | | |&#8226; |[зависит от](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Отслеживание изменений](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[зависит от](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Контейнеры](containers.md) | Windows и Linux | &#8226; | &#8226; |  |  |  | 3 минуты |
| [Анализ хранилища ключей](azure-key-vault.md) | Windows | | | | | |при уведомлении |
| [Оценка вредоносных программ](../../security-center/security-center-install-endpoint-protection.md) | Windows |&#8226; |&#8226; | | |&#8226; |Ежечасно |
| [Монитор производительности сети](network-performance-monitor.md) |  Windows | &#8226; | &#8226; |  |  |  | Подтверждения TCP выполняются каждые 5 секунд, данные отправляются каждые 3 минуты |
| [Аналитика Office 365 (предварительная версия)](solution-office-365.md) | Windows | | | | | |при уведомлении |
| **Решение для мониторинга** | **Платформа** | **Microsoft Monitoring Agent** | **Агент Operations Manager** | **Служба хранилища Azure** | **Нужен ли Operations Manager?** | **Данные агента Operations Manager отправляются через группу управления** | **Частота сбора** |
| [Анализ Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) | Windows | | |&#8226; | | |5 мин |
| [Схема услуги](service-map.md) | Windows и Linux | &#8226; | &#8226; |  |  |  | 15 секунд |
| [Оценка SQL](sql-assessment.md) | Windows |&#8226; |&#8226; | | |&#8226; |7 дней |
| [SurfaceHub](surface-hubs.md) | Windows |&#8226; | | | | |При получении |
| [Оценка System Center Operations Manager (предварительная версия)](scom-assessment.md) |  Windows | &#8226; | &#8226; |  |  | &#8226; | 7 дней |
| [Управление обновлениями](../../automation/automation-update-management.md) |  Windows |&#8226; |&#8226; | | |&#8226; |не меньше 2 раз в день и через 15 минут после установки обновления |
| [Готовность к обновлению](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) |  Windows | &#8226; |  |  |  |  | 2 дня |
| [Мониторинг VMware (не рекомендуется)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 минуты |
| [Wire Data 2.0 (предварительная версия)](wire-data.md) |Windows (2012 R2/8.1 или более поздней версии) |&#8226; |&#8226; | | | | 1 минута |




## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [Установка и использование решений для мониторинга](solutions.md).
* Узнайте из статьи [Анализ данных Log Analytics в Azure Monitor](../log-query/log-query-overview.md), как создавать запросы для анализа данных, собранных решением для мониторинга.
