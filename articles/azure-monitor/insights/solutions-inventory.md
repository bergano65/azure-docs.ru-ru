---
title: Инвентаризация решений мониторинга в Azure | Документация Майкрософт
description: Решения мониторинга в Azure Monitor — это коллекция правил логики, визуализации и получения данных, которые предоставляют метрики, связанные с определенной проблемной областью.  В этой статье представлен список решений по мониторингу, доступных в корпорации Майкрософт, а также сведения о методах и частоте сбора данных.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: article
author: MGoedtel
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 1f93ada1a6fb6b87c79a3713e80ebfefae9fe56f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900637"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Сведения об инвентаризации и сборе данных для решений мониторинга в Azure
[Решения для мониторинга](solutions.md) используют службы в Azure для предоставления дополнительных сведений об операциях конкретного приложения или службы. Как правило, решения мониторинга собирают данные журналов, а также предоставляют запросы и представления для анализа собранных данных. В Azure Monitor вы можете добавить решения мониторинга для всех используемых приложений и служб. Обычно они предоставляются бесплатно, но сбор данных может повлечь определенную плату за использование ресурсов.

Эта статья содержит список [решений монтиоринг](solutions.md) , доступных в корпорации Майкрософт, со ссылками на подробную документацию по.  Здесь вы также найдете сведения о методах и частоте сбора данных в Azure Monitor.  Сведения, приведенные в этой статье, можно использовать для определения различных доступных решений, а также для понимания требований к потокам данных и подключениям для различных решений мониторинга.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Список решений для мониторинга

В следующей таблице перечислены [решения мониторинга](solutions.md) в Azure, предоставляемые корпорацией Майкрософт. Отметка в столбце означает, что конкретное решение использует соответствующий метод для сбора данных в Azure Monitor.  Те решения, для которых не отмечен ни один столбец, записывают данные в Azure Monitor напрямую из другой службы Azure. Ссылки, предложенные для каждого из решений, ведут на подробную документацию, где вы можете получить дополнительную информацию.

Ниже перечислены столбцы этой таблицы с пояснениями.

- **Агент Microsoft Monitoring Agent** , используемый в Windows и Linux для запуска пакета управления из SCOM и мониторинга решений из Azure. В этой конфигурации агент подключается напрямую к Azure Monitor, не используя группу управления Operations Manager. 
- **Operations Manager** — это точно такой же агент, как Microsoft Monitoring Agent. Но в этой конфигурации он [подключается к группе управления Operations Manager](../platform/om-agents.md), которая, в свою очередь, подключена к Azure Monitor. 
-  **Хранилище Azure** означает, что решение собирает данные через учетную запись хранения Azure. 
- **Нужен ли Operations Manager?** — Для сбора данных решением по мониторингу требуется подключенная группа управления Operations Manager. 
- **Данные агента Operations Manager отправляются через группу управления**. Если агент [подключен к группе управления SCOM](../platform/om-agents.md), то он отправляет данные в Azure Monitor с сервера управления. В этой конфигурации агенту не требуется прямое подключение к службе Azure Monitor. Если в этом поле нет отметки, все данные отправляются от агента напрямую в Azure Monitor даже в том случае, если агент подключен к группе управления SCOM. Это означает, что ему необходима возможность взаимодействовать с Log Analytics с помощью [шлюза Azure Monitor](../platform/gateway.md).
- **Частота сбора** — указывает частоту, с которой данные собираются решением мониторинга. 



| **Решение для мониторинга** | **Платформа** | **Microsoft Monitoring Agent** | **Агент Operations Manager** | **служба хранилища Azure** | **Нужен ли Operations Manager?** | **Данные агента Operations Manager отправляются через группу управления** | **Частота сбора** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Аналитический журнал действий](../platform/activity-log-collect.md) | Azure | | | | | | при уведомлении |
| [Оценка AD](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 дней |
| [Состояние репликации AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 дней |
| [Работоспособность агентов](solution-agenthealth.md) | Windows и Linux | &#8226; | &#8226; | | | &#8226; | 1 мин |
| [Управление оповещениями](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |При получении |
| [Управление оповещениями](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 мин |
| [Управление оповещениями](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 минуты |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | Н/Д |
| [Соединитель Application Insights (не рекомендуется)](../platform/app-insights-connector.md) | Azure | | | |  |  | при уведомлении |
| [Гибридная рабочая роль службы автоматизации](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Н/Д |
| [Анализ шлюзов приложений Azure](azure-networking-analytics.md) | Azure |  |  |  |  |  | при уведомлении |
| **Решение для мониторинга** | **Платформа** | **Microsoft Monitoring Agent** | **Агент Operations Manager** | **служба хранилища Azure** | **Нужен ли Operations Manager?** | **Данные агента Operations Manager отправляются через группу управления** | **Частота сбора** |
| [Аналитика групп безопасности сетей Azure (не рекомендуется)](azure-networking-analytics.md) | Azure |  |  |  |  |  | при уведомлении |
| [Службы анализа SQL Azure (предварительная версия)](azure-sql.md) | Windows | | | | | | 1 мин |
| [Azure Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | при уведомлении |
| [Емкость и производительность (предварительная версия)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |При получении |
| [Отслеживание изменений](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[различать](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Отслеживание изменений](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[различать](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Контейнеры](containers.md) | Windows и Linux | &#8226; | &#8226; |  |  |  | 3 минуты |
| [Аналитика Key Vault](azure-key-vault.md) |Windows | | | | | |при уведомлении |
| [Оценка вредоносных программ](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |Ежечасно |
| [Монитор производительности сети](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Подтверждения TCP выполняются каждые 5 секунд, данные отправляются каждые 3 минуты |
| [Аналитика Office 365 (предварительная версия)](solution-office-365.md) |Windows | | | | | |при уведомлении |
| **Решение для мониторинга** | **Платформа** | **Microsoft Monitoring Agent** | **Агент Operations Manager** | **служба хранилища Azure** | **Нужен ли Operations Manager?** | **Данные агента Operations Manager отправляются через группу управления** | **Частота сбора** |
| [Аналитика Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 мин |
| [Схема услуги](service-map.md) | Windows и Linux | &#8226; | &#8226; |  |  |  | 15 секунд |
| [Оценка SQL](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 дней |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |При получении |
| [Оценка System Center Operations Manager (предварительная версия)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7 дней |
| [Управление обновлениями](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |не меньше 2 раз в день и через 15 минут после установки обновления |
| [Готовность к обновлению](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 дня |
| [Мониторинг VMware (не рекомендуется)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 минуты |
| [Wire Data 2.0 (предварительная версия)](wire-data.md) |Windows (2012 R2/8.1 или более поздней версии) |&#8226; |&#8226; | | | | 1 мин |




## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [устанавливать и использовать решения для мониторинга](solutions.md).
* Узнайте из статьи [Анализ данных Log Analytics в Azure Monitor](../log-query/log-query-overview.md), как создавать запросы для анализа данных, собранных решением для мониторинга.
