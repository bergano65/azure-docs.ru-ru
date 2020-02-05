---
title: Azure Monitor вопросы и ответы | Документация Майкрософт
description: Ответы на часто задаваемые вопросы о Azure Monitor.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/23/2020
ms.openlocfilehash: 9f377f93ab8fef2c1ad713da6fcd6c6f14107c3f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986824"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor часто задаваемые вопросы

Этот список часто задаваемых вопросов о Azure Monitor см. в этой статье.

## <a name="general"></a>Общие

### <a name="what-is-azure-monitor"></a>Общие сведения об Azure Monitor
[Azure Monitor](overview.md) — это служба в Azure, которая обеспечивает мониторинг производительности и доступности для приложений и служб в Azure, в других облачных средах или в локальной среде. Azure Monitor собирает данные из нескольких источников в общую платформу данных, где их можно проанализировать на предмет тенденций и аномалий. Широкие возможности Azure Monitor помогают быстро определять и реагировать на критические ситуации, которые могут повлиять на работу приложения.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>В чем разница между Azure Monitor, Log Analytics и Application Insights?
В сентябре 2018 Корпорация Майкрософт объединила Azure Monitor, Log Analytics и Application Insights в одну службу, чтобы обеспечить полнофункциональный сквозной мониторинг приложений и компонентов, на которых они основаны. Функции в Log Analytics и Application Insights не изменились, хотя некоторые функции были изменены с учетом Azure Monitor для лучшего отражения их новой области. Обработчик данных журнала и язык запросов Log Analytics теперь называются Azure Monitor журналами. См. [Azure Monitor обновления терминологии](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Что Azure Monitor затраты?
Функции Azure Monitor, которые автоматически включены, например сбор метрик и журналов действий, предоставляются бесплатно. Существуют затраты, связанные с другими функциями, такими как запросы журналов и оповещения. Подробные сведения о ценах см. на [странице цен на Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) .

### <a name="how-do-i-enable-azure-monitor"></a>Разделы справки включить Azure Monitor?
Azure Monitor включается, когда вы создаете новую подписку Azure, и [метрики](platform/data-platform-metrics.md) [журнала действий](platform/activity-logs-overview.md) и платформы автоматически собираются. Создайте [параметры диагностики](platform/diagnostic-settings.md) для сбора более подробных сведений о работе ресурсов Azure и добавьте [решения для мониторинга](insights/solutions.md) и аналитические сведения [, чтобы обеспечить](insights/insights-overview.md) дополнительный анализ собранных данных для конкретных служб. 

### <a name="how-do-i-access-azure-monitor"></a>Azure Monitor Разделы справки доступа?
Доступ ко всем Azure Monitor функциям и данным из меню " **монитор** " в портал Azure. Раздел **мониторинг** в меню для различных служб Azure предоставляет доступ к тем же средствам с данными, отфильтрованными для определенного ресурса. Azure Monitor данные также доступны для различных сценариев с помощью интерфейса командной строки, PowerShell и REST API.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Существует ли локальная версия Azure Monitor?
Нет. Azure Monitor — это масштабируемая облачная служба, которая обрабатывает и хранит большие объемы данных, хотя Azure Monitor может отслеживать ресурсы, которые находятся в локальной среде и в других облаках.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Может Azure Monitor отслеживать локальные ресурсы?
Да, в дополнение к сбору данных мониторинга из ресурсов Azure Azure Monitor может собирать данные из виртуальных машин и приложений в других облаках и в локальной среде. См. раздел [Источники данных мониторинга для Azure Monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Azure Monitor интегрируется с System Center Operations Manager?
Вы можете подключить существующую группу управления System Center Operations Manager к Azure Monitor, чтобы получать данные из агентов в журналы Azure Monitor. Это позволяет использовать запросы и решения журнала для анализа данных, собираемых из агентов. Можно также настроить существующие агенты System Center Operations Manager для отправки данных непосредственно в Azure Monitor. См. раздел [Connect Operations Manager to Azure Monitor](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Какие IP-адреса Azure Monitor использовать?
Список IP-адресов и портов, необходимых агентам и другим внешним ресурсам для доступа к Azure Monitor, см. в разделе [IP-адреса, используемые Application Insights и log Analytics](app/ip-addresses.md) . 

## <a name="monitoring-data"></a>Данные мониторинга

### <a name="where-does-azure-monitor-get-its-data"></a>Где Azure Monitor получить данные?
Azure Monitor собирает данные из различных источников, включая журналы и метрики из платформы и ресурсов Azure, пользовательские приложения и агенты, работающие на виртуальных машинах. Другие службы, такие как центр безопасности Azure и наблюдатель за сетями, собираются данные в Log Analytics рабочей области, чтобы их можно было проанализировать с помощью Azure Monitor данных. Вы также можете отправить пользовательские данные в Azure Monitor с помощью REST API для журналов или метрик. См. раздел [Источники данных мониторинга для Azure Monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Какие данные собираются с Azure Monitor? 
Azure Monitor собирает данные из различных источников в [журналы](platform/data-platform-logs.md) или [метрики](platform/data-platform-metrics.md). Каждый тип данных имеет свои собственные относительные преимущества, каждый из которых поддерживает определенный набор функций в Azure Monitor. Для каждой подписки Azure существует одна база данных метрик, в то время как вы можете создать несколько Log Analytics рабочих областей для сбора журналов в зависимости от ваших требований. См. статью [Azure Monitor Data Platform](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Существует ли максимальный объем данных, которые можно получить в Azure Monitor?
Количество данных метрик, которые можно собираются, не ограничено, но эти данные хранятся не более 93 дней. См. раздел [Хранение метрик](platform/data-platform-metrics.md#retention-of-metrics). Объем данных журнала, которые можно собирать, не ограничен, но на него может влиять ценовая категория, выбранная для Log Analytics рабочей области. См. сведения о [ценах](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Разделы справки получить доступ к данным, собранным Azure Monitor?
Аналитические сведения и решения предоставляют пользовательский интерфейс для работы с данными, хранящимися в Azure Monitor. Вы можете работать непосредственно с данными журнала, используя запрос журнала, написанный на языке запросов Kusto (ККЛ). В портал Azure можно создавать и выполнять запросы и интерактивно анализировать данные с помощью Log Analytics. Анализируйте метрики в портал Azure с обозреватель метрик. См. статью [анализ данных журнала в Azure Monitor](log-query/log-query-overview.md) и начало [работы с обозреватель метрик Azure](platform/metrics-getting-started.md).





## <a name="solutions-and-insights"></a>Решения и аналитические сведения

### <a name="what-is-an-insight-in-azure-monitor"></a>Что такое Azure Monitor?
Insights предоставляет настраиваемые возможности мониторинга для конкретных служб Azure. Они используют те же метрики и журналы, что и другие функции в Azure Monitor но могут собираются дополнительные данные и обеспечивают уникальную работу в портал Azure. Ознакомьтесь [с подробностями в Azure Monitor](insights/insights-overview.md).

Просмотреть аналитические сведения в портал Azure можно в разделе " **аналитика** " меню " **монитор** " или в разделе " **мониторинг** " меню службы.

### <a name="what-is-a-solution-in-azure-monitor"></a>Что такое решение в Azure Monitor?
Решения для мониторинга представляют собой упакованные наборы логики для мониторинга определенного приложения или службы на основе Azure Monitorных функций. Они собираются данные журнала в Azure Monitor и предоставляют запросы и представления журналов для их анализа с помощью общего интерфейса в портал Azure. См. раздел [мониторинг решений в Azure Monitor](insights/solutions.md).

Чтобы просмотреть решения в портал Azure, в разделе " **аналитика** " в меню " **мониторинг** " выберите пункт **Дополнительно** . Нажмите кнопку **Добавить** , чтобы добавить дополнительные решения в рабочую область.






## <a name="logs"></a>Журналы

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>В чем разница между журналами Azure Monitor и Azure обозреватель данных?
Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Журналы Azure Monitor построены на основе обозреватель данных Azure и используют тот же язык запросов Kusto (ККЛ) с некоторыми незначительными различиями. См. раздел [отличия языка запросов Azure Monitor](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Разделы справки получить данные журнала?
Все данные извлекаются из рабочей области Log Analytics с помощью запроса журнала, написанного с помощью языка запросов Kusto (ККЛ). Вы можете создавать собственные запросы или использовать решения и аналитические сведения, включающие запросы журналов для конкретного приложения или службы. См. раздел [Обзор запросов журнала в Azure Monitor](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>Что такое Рабочая область Log Analytics?
Все данные журнала, собранные Azure Monitor, хранятся в Log Analytics рабочей области. Рабочая область по сути является контейнером, в котором данные журнала собираются из различных источников. У вас может быть одна рабочая область Log Analytics для всех данных мониторинга или требования к нескольким рабочим областям. См. раздел [проектирование развертывания журналов Azure Monitor](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Можно ли переместить существующую рабочую область Log Analytics в другую подписку Azure?
Рабочую область можно перемещать между группами ресурсов или подписками, но не в другой регион. См. статью [перемещение log Analytics рабочей области в другую подписку или группу ресурсов](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Почему в Log Analytics не отображаются кнопки "Обозреватель запросов" и "Сохранить"?

Кнопки **Обозреватель запросов**, **Сохранение** и **новые правила оповещений** недоступны, если для [области запроса](log-query/scope.md) задан конкретный ресурс. Чтобы создать предупреждения, сохраните или загрузите запрос, Log Analytics должны быть ограничены рабочей областью. Чтобы открыть Log Analytics в контексте рабочей области, выберите **журналы** в меню **Azure Monitor** . Выбирается последняя использованная рабочая область, но вы можете выбрать и любую другую рабочую область. Ознакомьтесь с [областью запросов журнала и диапазоном времени в Azure Monitor log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Почему я получаю сообщение об ошибке: "Регистрация поставщика ресурсов Microsoft. Insights" для этой подписки, чтобы включить этот запрос при открытии Log Analytics из виртуальной машины? 
Многие поставщики ресурсов регистрируются автоматически, но может потребоваться вручную зарегистрировать некоторые поставщики ресурсов. Областью регистрации всегда является подписка. См. дополнительные сведения о [поставщиках и типах ресурсов](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Почему при открытии Log Analytics из виртуальной машины не появляется сообщение об ошибке доступа? 
Чтобы просмотреть журналы виртуальных машин, необходимо предоставить разрешение на чтение рабочим областям, в которых хранятся журналы виртуальных машин. В таком случае администратору необходимо предоставить вам разрешения в Azure.




## <a name="alerts"></a>Предупреждения

### <a name="what-is-an-alert-in-azure-monitor"></a>Что такое предупреждение в Azure Monitor?
С помощью оповещений можно заранее получать уведомления о важных условиях, обнаруженных в ваших данных мониторинга. Они позволяют выявить и устранить проблемы, прежде чем пользователи системы обнаружат их. Существует несколько видов оповещений.

- Метрика-метрика превышает пороговое значение.
- Журнал запросов — результаты запроса журнала, соответствующие заданным условиям.
- Журнал действий — событие журнала действий соответствует заданным условиям.
- Веб-тест — результаты определенных критериев соответствия теста доступности.


См. раздел [Обзор оповещений в Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Что такое группа действий?
Группа действий — это коллекция уведомлений и действий, которые могут быть активированы предупреждением. Несколько предупреждений могут использовать одну группу действий, что позволяет использовать общие наборы уведомлений и действий. См. раздел [Создание групп действий и управление ими в портал Azure](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Что такое правило действия?
Правило действия позволяет изменить поведение набора предупреждений, соответствующих определенным критериям. Это позволяет выполнять такие требования, как отключение действий предупреждений в течение периода обслуживания. Вы также можете применить группу действий к набору предупреждений вместо того, чтобы применять их непосредственно к правилам генерации оповещений. См. [правила действий](platform/alerts-action-rules.md).


## <a name="agents"></a>Агенты

### <a name="does-azure-monitor-require-an-agent"></a>Azure Monitor требуется агент?
Агент требуется только для получения данных из операционной системы и рабочих нагрузок на виртуальных машинах. Виртуальные машины можно найти в Azure, в другой облачной среде или локально. См. раздел [Общие сведения об агентах Azure Monitor](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>В чем разница между агентами Azure Monitor?
Расширение диагностики Azure предназначено для виртуальных машин Azure и собирает данные для Azure Monitor метрик, службы хранилища Azure и концентраторов событий Azure. Агент Log Analytics предназначен для виртуальных машин в Azure, другой облачной среде или локальной сети и собирает данные для Azure Monitor журналов. Агенту зависимостей требуется агент Log Analytics и собранные сведения о процессе и зависимости. См. раздел [Общие сведения об агентах Azure Monitor](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Используется ли в моем трафике агента подключение ExpressRoute?
Трафик к Azure Monitor использует канал ExpressRoute пиринга Майкрософт. Описание различных типов трафика ExpressRoute см. в [документации по expressroute](../expressroute/expressroute-faqs.md#supported-services) . 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Как убедиться, что агент Log Analytics может взаимодействовать с Azure Monitor?
В панели управления на компьютере агента выберите **параметры & безопасности**, **Microsoft Monitoring Agent** . На вкладке **Azure log Analytics (OMS)** зеленый значок галочки подтверждает, что агент может взаимодействовать с Azure Monitor. Желтый значок предупреждения означает, что у агента возникли проблемы. Одна из распространенных причин — служба **Microsoft Monitoring Agent** остановлена. Перезапустите службу с помощью диспетчера управления службами.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Разделы справки ли Log Analytics агенту взаимодействовать с Azure Monitor?
Для агентов, подключенных к Log Analytics напрямую, откройте панель управления и выберите **Параметры безопасности &** **Microsoft Monitoring Agent**. На вкладке **Azure log Analytics (OMS)** удалите все рабочие области, перечисленные в списке. В System Center Operations Manager удалите компьютер из списка управляемые компьютеры Log Analytics. Operations Manager обновит конфигурацию агента, чтобы больше не отправлять отчеты в Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Какой объем данных отправляет каждый агент?
Объем данных, отправляемых каждым агентом, зависит от:

* решения, которое вы используете;
* количества журналов и счетчиков производительности, которые вы собираете;
* объема данных в журналах.

Дополнительные сведения см. [в статье Управление использованием и затратами с помощью журналов Azure Monitor](platform/manage-cost-storage.md) .

Для компьютеров, на которых может запускаться агент WireData, объем отправляемых данных можно просмотреть с помощью следующего запроса:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Какой объем пропускной способности сети используется агентом управления Майкрософт (MMA) при отправке данных в Azure Monitor?
Полоса пропускания — это функция от объема отправляемых данных. При передаче по сети данные сжимаются.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Как можно получать уведомления о прекращении сбора данных из агента Log Analytics?

Выполните действия, описанные в разделе о [создании оповещений журналов](platform/alerts-metric.md), чтобы получать уведомления при прекращении сбора данных. Используйте следующие параметры для правила оповещения:

- **Определение условия оповещения**. Укажите рабочую область log Analytics в качестве цели ресурса.
- **Критерии оповещений** 
   - **Имя сигнала**: *Пользовательский поиск по журналам*
   - **Поисковый запрос**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Логика оповещения**: **на основе** *числа результатов*, **условия** *больше*, **порогового значения** *0*
   - **Вычисляется на основе**: **период (в минутах)** *30*, **Частота (в минутах)** *10*
- **Определение сведений о предупреждении** 
   - **Имя**: *сбор данных остановлен*
   - **Серьезность**: *предупреждение*

Укажите существующую или новую [группу действий](platform/action-groups.md) , чтобы при условии, что оповещение журнала соответствует условиям, вы будете уведомлены об отсутствии пульса в течение более чем 15 минут.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Каковы требования к брандмауэру для Azure Monitor агентов?
Дополнительные сведения о требованиях брандмауэра см. в статье [требования к сетевому брандмауэру](platform/log-analytics-agent.md#network-firewall-requirements).


## <a name="visualizations"></a>Визуализации

### <a name="why-cant-i-cant-see-view-designer"></a>Почему конструктор представлений не отображается?

Конструктор представлений доступен только для пользователей, которым назначены разрешения участников или выше в рабочей области Log Analytics.


## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Проблемы с конфигурацией
*У меня не получается настроить компоненты, о которых идет речь в таких статьях:*

* [Troubleshooting no data - Application Insights for .NET](app/asp-net-troubleshoot-no-data.md)
* [раздел "Устранение неполадок"](app/monitor-performance-live-website-now.md#troubleshoot)
* [Настройка системы диагностики Azure для входа в Application Insights](platform/diagnostics-extension-to-application-insights.md)
* [Веб-приложение Java](app/java-troubleshoot.md)

*Я не получаю данные с моего сервера*

* [Настройка исключений брандмауэра](app/ip-addresses.md)
* [Настройка сервера ASP.NET](app/monitor-performance-live-website-now.md)
* [Настройка сервера Java](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Можно ли использовать Application Insights с...?

* [Веб-приложения на сервере IIS в ВИРТУАЛЬНОЙ машине Azure или в масштабируемом наборе виртуальных машин Azure](app/azure-vm-vmss-apps.md)
* [Веб-приложения на сервере IIS — локальном или в виртуальной машине](app/asp-net.md)
* [Веб-приложения Java](app/java-get-started.md)
* [Приложения Node.js](app/nodejs.md)
* [Веб-приложения в Azure](app/azure-web-apps.md)
* [Облачные службы в Azure](app/cloudservices.md)
* [Серверы приложений, работающие в Docker](app/docker.md)
* [Одностраничные веб-приложения](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Классические приложения Windows](app/windows-desktop.md)
* [другие платформы.](app/platforms.md)

### <a name="is-it-free"></a>Предоставляется ли бесплатно?

Да, для экспериментальных целей. По условиям тарифного плана "Базовый" приложение может отправлять определенный лимит данных каждый месяц бесплатно. Бесплатный лимит достаточен для разработки и публикации приложения для небольшого числа пользователей. Можно задать ограничение, запрещающее обработку данных сверх заданного лимита.

Оплата за большие объемы данных телеметрии взимается по ГБ. Мы даем ряд советов по тому, как [ограничить расходы](app/pricing.md).

В рамках плана "Корпоративный" оплата взимается за каждый день, в который каждый узел веб-сервера отправляет телеметрию. Он подходит для случаев, когда требуется использовать непрерывный экспорт в большом масштабе.

[Изучите тарифный план](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Сколько это стоит?

* Откройте страницу **Usage and estimated costs** (Данные об использовании и предполагаемые расходы) для ресурса Application Insights. На ней приводится диаграмма недавнего использования. При желании можно задать ограничение на объем данных.
* Откройте [колонку подписок и выставления счетов Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview), чтобы просмотреть счета за все ресурсы.

### <a name="q14"></a>Что Application Insights изменяет в моем проекте?
Подробности зависят от типа проекта. Для веб-приложения:

* Добавляет в проект следующие файлы:
  * ApplicationInsights.config
  * ai.js
* Устанавливает следующие пакеты NuGet:
  * *Application Insights API* — основной API
  * *API Application Insights для веб-приложений* — используется для отправки данных телеметрии со стороны сервера
  * *API Application Insights для приложений JavaScript* — используется для отправки данных телеметрии со стороны клиента
* Пакет включает эти сборки:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Вставляет элементы в:
  * Файл Web.config
  * packages.config
* (Только для новых проектов. при [добавлении Application Insights в существующий проект][start]это необходимо сделать вручную.) Вставляет фрагменты кода в клиент и серверный код, чтобы инициализировать их с помощью идентификатора ресурса Application Insights. Например, в приложении MVC код вставляется в представления главной страницы/Shared/\_Layout. cshtml.

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Как обновить предыдущие версии пакета SDK?
Информацию для пакета SDK, соответствующего типу вашего приложения, см. в статье с [заметками о выпуске](app/release-notes.md).

### <a name="update"></a>Как изменить ресурс Azure, в который проект отправляет данные?
В обозревателе решений щелкните правой кнопкой мыши `ApplicationInsights.config` и выберите **Обновить Application Insights**. Данные можно отправлять на существующий или новый ресурс в Azure. Мастер обновления изменяет ключ инструментирования в файле ApplicationInsights.config, который определяет, куда сервер SDK должен отправлять ваши данные. Если отменить выбор параметра "Обновить все", это также приведет к изменению ключа, отображаемого на ваших веб-страницах.

### <a name="what-is-status-monitor"></a>Что такое монитор состояния?

Это классическое приложение, которое можно использовать на веб-сервере IIS для настройки Application Insights в веб-приложениях. Оно не собирает телеметрию: его можно остановить, когда вы не настраиваете приложение. 

[Дополнительные сведения](app/monitor-performance-live-website-now.md#questions)

### <a name="what-telemetry-is-collected-by-application-insights"></a>Какую телеметрию собирает Application Insights?

Из серверных веб-приложений:

* HTTP-запросы
* [зависимости](app/asp-net-dependencies.md); вызовы к базам данных SQL, Azure Cosmos DB, таблице, хранилищу BLOB-объектов и очереди; HTTP-вызовы к внешним службам; 
* [исключения](app/asp-net-exceptions.md) и трассировки стека;
* [Счетчики производительности](app/performance-counters.md) — при использовании [Монитор состояния](app/monitor-performance-live-website-now.md), [мониторинга Azure для служб приложений](app/azure-web-apps.md), [мониторинга Azure для виртуальной машины или масштабируемого набора виртуальных машин](app/azure-vm-vmss-apps.md)или [Application Insights собранного модуля записи](app/java-collectd.md).
* [пользовательские события и метрики](app/api-custom-events-metrics.md), которые вы создаете в коде;
* [журналы трассировки](app/asp-net-trace-logs.md), если вы настраиваете соответствующий сборщик.

С [клиентских веб-страниц](app/javascript.md):

* [число просмотров страниц](app/usage-overview.md);
* [вызовы AJAX](app/asp-net-dependencies.md) — запросы, выполняемые из запущенного скрипта;
* загрузка данных при просмотре страниц;
* количество пользователей и сеансов;
* [идентификаторы пользователей, прошедших проверку подлинности](app/api-custom-events-metrics.md#authenticated-users).

Из других источников, если они настроены:

* [Настройка системы диагностики Azure для входа в Application Insights](platform/diagnostics-extension-to-application-insights.md)
* [Импорт в службу аналитики](platform/data-collector-api.md)
* [Служба Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md).

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Можно ли отфильтровать или изменить некоторые данные телеметрии?

Да, на сервере можно создать следующие компоненты:

* обработчик данных телеметрии для фильтрации или добавления свойств в выбранные элементы телеметрии перед их отправкой из приложения;
* инициализатор телеметрии для добавления свойств во все элементы телеметрии.

Дополнительные сведения об [ASP.NET](app/api-filtering-sampling.md) или [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Как рассчитывается город, страна или регион и другие данные географического расположения?

Мы определяем IP-адрес (IPv4 или IPv6) веб-клиента с помощью [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Телеметрия браузера: мы собираем сведения об IP-адресе отправителя.
* Телеметрия сервера: модуль Application Insights собирает сведения об IP-адресе клиента. Эти сведения не собираются, если задан заголовок `X-Forwarded-For`.
* Дополнительные сведения о сборе IP-адресов и данных географического расположения в Application Insights см. в этой [статье](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


Вы можете настроить `ClientIpHeaderTelemetryInitializer` для получения IP-адреса из другого заголовка. Например, в некоторых системах он переносится прокси-сервером, балансировщиком нагрузки или сетью CDN в `X-Originating-IP`. [Дополнительные сведения](https://apmtips.com/blog/2016/07/05/client-ip-address/)

Вы можете [использовать Power BI](app/export-power-bi.md ) для отображения данных телеметрии по запросам на карте.


### <a name="data"></a>Как долго данные хранятся на портале? Защищены ли они?
Ознакомьтесь с [хранением и конфиденциальностью данных][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Что происходит с данными телеметрии Application Insights, когда сервер или устройство теряет подключение к Azure?

Все наши пакеты SDK, включая веб-пакет SDK, включают в себя "надежный транспорт" или "надежный транспорт". Когда сервер или устройство теряет подключение к Azure, данные телеметрии [хранятся локально в файловой системе](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) (пакеты SDK сервера) или в хранилище сеансов HTML5 (веб-пакет SDK). Пакет SDK будет периодически повторять попытку отправки данных телеметрии до тех пор, пока служба приема не считает его "устаревшим" (48 часов для журналов, 30 минут для метрик). Устаревшие данные телеметрии будут удалены. В некоторых случаях, например при заполнении локального хранилища, повторная попытка не выполняется.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Могут ли персональные данные отправляться с данными телеметрии?

Это возможно, если ваш код отправляет такие данные. Это также может происходить, если переменные в трассировках стека содержат персональные данные. Ваша команда разработчиков должна произвести оценку рисков, чтобы обеспечить надлежащую обработку персональных данных. [Дополнительные сведения о хранении и конфиденциальности данных](app/data-retention-privacy.md).

Для **всех** октетов веб-адреса клиента всегда задается значение 0 после подстановки атрибутов географического расположения.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Мой ключ инструментирования отображается в моем источнике веб-страницы. 

* Это обычная ситуация в решениях для мониторинга.
* С его помощью нельзя похитить ваши данные.
* Он может использоваться для искажения данных или активации оповещений.
* Мы не получали сообщений от клиентов о возникновении таких проблем.

Можно выполнить следующее.

* Используйте два отдельных ключа инструментирования (отдельные ресурсы Application Insights) для данных клиента и сервера. либо
* создать прокси-сервер, работающий на вашем сервере, и передавать данные веб-клиента через него.

### <a name="post"></a>Как просмотреть данные POST в колонке «Поиск по журналу диагностики»?
Данные POST не регистрируются автоматически, но можно использовать вызов TrackTrace. Для этого добавьте данные в параметр сообщения. В нем можно добавить больше символов, чем в свойствах строк, но по нему невозможно выполнить фильтрацию.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Следует ли использовать один или несколько ресурсов Application Insights?

Используйте один ресурс для всех компонентов или ролей в единой бизнес-системе. Используйте отдельные ресурсы для стадий разработки, тестирования и выпуска, а также для независимых приложений.

* [Ознакомьтесь с этим обсуждением](app/separate-resources.md)
* [Пример: облачная служба с рабочей ролью и веб-ролью](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Как динамически изменить ключ инструментирования?

* [Ознакомьтесь с этим обсуждением](app/separate-resources.md)
* [Пример: облачная служба с рабочей ролью и веб-ролью](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Что такое количество пользователей и сеансов?

* Пакет SDK для JavaScript создает файл cookie пользователя в веб-клиенте для определения возвращающихся пользователей и файл cookie сеанса для группировки действий.
* Если скрипт на стороне клиента отсутствует, вы можете [задать файлы cookie на сервере](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Если один реальный пользователь работает с вашим сайтом в разных браузерах, на разных компьютерах либо использует конфиденциальный режим просмотра или режим инкогнито, то он будет учитываться несколько раз.
* Для определения вошедшего в систему пользователя на разных компьютерах и в разных браузерах добавьте вызов [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="q17"></a> Все ли активировано в Application Insights?
| Что вы должны видеть | Как это получить | Для чего это нужно |
| --- | --- | --- |
| Диаграммы доступности |[Веб-тесты](app/monitor-web-app-availability.md) |Узнать, что ваше веб-приложение работает |
| Производительность приложения на сервере: время отклика и т.д. |[Добавить Application Insights в ваш проект](app/asp-net.md) или [установить монитор состояний Application Insights на сервере](app/monitor-performance-live-website-now.md) (или написать собственный код для [отслеживания зависимостей](app/api-custom-events-metrics.md#trackdependency)) |Выявить проблемы производительности |
| Телеметрия зависимостей |[Установить монитор состояний Application Insights на сервере](app/monitor-performance-live-website-now.md) |Выявить проблемы с базами данных или другими внешними компонентами |
| Получение данных трассировки стека из исключений |[Вставить вызовы TrackException в код](app/asp-net-exceptions.md) (некоторые выводятся автоматически) |Обнаружить и диагностировать исключения |
| Поиск по трассировкам журнала |[Добавить адаптер ведения журнала](app/asp-net-trace-logs.md) |Выявить исключения, проблемы производительности |
| Основная информация об использовании клиента: просмотр страниц, сеансы и т. д. |[Инициализатор JavaScript на веб-страницах](app/javascript.md) |Аналитика использования |
| Настраиваемые метрики клиента |[Трассировка вызовов на веб-страницах](app/api-custom-events-metrics.md) |Расширить возможности для пользователя |
| Настраиваемые метрики сервера |[Отслеживание вызовов на сервере](app/api-custom-events-metrics.md) |Бизнес-аналитика |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Почему счетчики в результатах поиска и на диаграммах метрик не совпадают?

[Выборка](app/sampling.md) сокращает число элементов телеметрии (запросов, пользовательских событий и т. д.), которые передаются из приложения на портал. В результатах поиска вы видите число фактически полученных элементов. На диаграммах метрик, на которых приводится число событий, вы видите количество исходных событий, которые произошли. 

Каждый передаваемый элемент имеет свойство `itemCount`, которое показывает, сколько исходных событий представляет этот элемент. Чтобы увидеть, как работает выборка, можно выполнить в Analytics следующий запрос:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Автоматизация

#### <a name="configuring-application-insights"></a>Настройка Application Insights

С помощью монитора ресурсов Azure можно [создавать скрипты PowerShell](app/powershell.md) для выполнения следующих задач:

* создание и обновление ресурсов Application Insights;
* задание ценового плана;
* получение ключа инструментирования;
* добавление оповещения метрики;
* добавление теста доступности.

Вы не можете настроить отчет обозревателя метрик или непрерывный экспорт.

#### <a name="querying-the-telemetry"></a>Запрос телеметрии

Используйте [REST API](https://dev.applicationinsights.io/) для выполнения запросов [аналитики](app/analytics.md).

### <a name="how-can-i-set-an-alert-on-an-event"></a>Как можно настроить оповещение о событии?

Оповещения Azure настраиваются только для метрик. Создайте пользовательскую метрику, пороговое значение которой будет нарушаться при наступлении события. Затем настройте оповещение для метрики. Вы получите уведомление каждый раз, когда метрика пересекает пороговое значение в любом направлении; уведомление не будет получено до первого пересечения, независимо от того, является ли начальное значение высоким или низким; задержка всегда составляет несколько минут.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Взимается ли плата за передачу данных между веб-приложением Azure и Application Insights?

* Если ваше веб-приложение Azure размещается в центре данных с конечной точкой сбора Application Insights, плата не взимается. 
* Если в центре данных размещения нет конечной точки сбора, то за передачу данных телеметрии приложения будет взиматься [плата за передачу исходящих данных Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Это не зависит от того, где размещается ресурс Application Insights. Значение имеет только распределение наших конечных точек.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Можно ли отправлять данные телеметрии на портал Application Insights?

Мы рекомендуем использовать наши пакеты SDK, а также [API пакета SDK](app/api-custom-events-metrics.md). Существуют разновидности пакетов SDK для разных [платформ](app/platforms.md). Эти пакеты SDK управляют буферизацией, сжатием, регулированием, повторными попытками и другими операциями. Однако [схема приема](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) и [протокол конечной точки](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) являются открытыми.

### <a name="can-i-monitor-an-intranet-web-server"></a>Можно ли отслеживать веб-сервер в интрасети?

Да, но вам потребуется разрешить службам использовать трафик путем внесения их в исключения брандмауэра или использовав перенаправления прокси-сервера.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


[Здесь](app/ip-addresses.md) можно проверить полный список служб и IP-адресов.

#### <a name="firewall-exception"></a>Исключение брандмауэра

Разрешите веб-серверу отправлять данные телеметрии в наши конечные точки. 

#### <a name="gateway-redirect"></a>Перенаправление шлюза

Маршрутизация трафика с сервера в шлюз интрасети выполняется путем перезаписи конечных точек в конфигурации. Если эти свойства "Конечная точка" отсутствуют в вашей конфигурации, эти классы будут использовать значения по умолчанию, показанные в примере ApplicationInsights.config, приведенном ниже. 

Шлюз должен выполнять маршрутизацию трафика, который идет к базовому адресу конечных точек. В файле настроек замените значение по умолчанию следующими параметрами `http://<your.gateway.address>/<relative path>`.


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Пример ApplicationInsights.config со значением по умолчанию для конечных точек.
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> Аппликатионидпровидер доступен начиная с версии 2.6.0.



#### <a name="proxy-passthrough"></a>Транзитный прокси-сервер

Пересылка прокси-сервера может быть достигнута путем настройки уровня компьютера или прокси уровня приложения.
Дополнительные сведения см. в статье DotNet в [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Пример файла Web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Можно ли выполнять веб-тесты доступности на сервере в интрасети?

Наши [веб-тесты](app/monitor-web-app-availability.md) выполняются в точках подключения, которые распределены по всему миру. Есть два решения.

* Разрешение в брандмауэре: разрешите передачу запросов к серверу от [агентов веб-тестирования из длинного меняющегося списка](app/ip-addresses.md).
* Напишите собственный код для периодической отправки запросов на сервер из интрасети. Для этой цели можно выполнять веб-тесты Visual Studio. Тест-инженер может отправлять результаты в Application Insights с помощью API TrackAvailability().

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Сколько времени требуется для сбора данных телеметрии?

Как правило, время сбора данных Application Insights не превышает 5 минут. Сбор некоторых данных (например, крупных файлов журналов) может занять больше времени. Дополнительные сведения см. в статье, посвященной [соглашениям об уровне обслуживания для Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md



## <a name="azure-monitor-for-containers"></a>Azure Monitor для контейнеров

В этом списке корпорация Майкрософт собрала часто задаваемые вопросы с ответами об Azure Monitor для контейнеров. Если у вас есть другие вопросы об этом решении, вы можете задать их на [форуме для обсуждений](https://feedback.azure.com/forums/34192--general-feedback). Если вопрос задается часто, мы добавим его в эту статью, чтобы его можно было найти быстро и легко.

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Не отображаются значения свойств Image и Name, заполненные при запросе таблицы Контаинерлог.

Для агента версии ciprod12042019 и более поздних версий по умолчанию эти два свойства не заполняются для каждой строки журнала, чтобы максимально сокращать затраты на собираемые данные журнала. Существует два варианта запроса таблицы, включающей эти свойства со своими значениями:

#### <a name="option-1"></a>Вариант 1 

Присоедините другие таблицы, чтобы включить эти значения свойств в результаты.

Измените запросы, включив свойства Image и Имажетаг из таблицы ```ContainerInventory```, присоединяясь к свойству ContainerID. Можно включить свойство Name (как оно было ранее появилось в ```ContainerLog``` таблице) из поля Контаиненаме таблицы Кубеподинвентори путем соединения по свойству ContainerID. Это рекомендуемый вариант.

Ниже приведен пример подробного запроса, посвященного получению значений полей с помощью соединений.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Вариант 2

Повторно включите коллекцию для этих свойств для каждой строки журнала контейнера.

Если первый вариант неудобен из-за изменений запросов, можно снова включить сбор этих полей, включив параметр ```log_collection_settings.enrich_container_logs``` в карте конфигурации агента, как описано в разделе [Параметры конфигурации сбора данных](insights/container-insights-agent-config.md).

> [!NOTE]
> Второй вариант не рекомендуется для больших кластеров, имеющих более 50 узлов, так как он создает вызовы сервера API из каждого узла в кластере для выполнения этого дополнения. Этот параметр также увеличивает размер данных для каждой собираемой строки журнала.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Можно ли просматривать метрики, собранные в Grafana?

Azure Monitor для контейнеров поддерживает просмотр метрик, хранящихся в рабочей области Log Analytics, на панелях мониторинга Grafana. Мы предоставили шаблон, который можно скачать из [репозитория панели мониторинга](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) Grafana, чтобы получить сведения о том, как запросить дополнительные данные из наблюдаемых кластеров для визуализации на пользовательских панелях мониторинга Grafana. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Можно ли отслеживать кластер AKS Engine с Azure Monitor для контейнеров?

Azure Monitor для контейнеров поддерживает мониторинг рабочих нагрузок контейнеров, развернутых в кластерах AKS Engine (ранее известных как ядро ACS), размещенных в Azure. Дополнительные сведения и общие сведения о шагах, необходимых для включения мониторинга в этом сценарии, см. в разделе [использование Azure Monitor для контейнеров для AKS-Engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Почему данные в рабочей области Log Analytics не отображаются?

Если вы не можете просматривать какие-либо данные в Log Analytics рабочей области в определенное время ежедневно, возможно, достигнуто ограничение в 500 МБ или ежедневное ограничение, заданное для управления объемом данных, которые будут ежедневно собраны. При достижении этого ограничения сбор данных прекращается и возобновляется только на следующий день. Чтобы просмотреть сведения об использовании данных и обновить их до другой ценовой категории в зависимости от ожидаемых моделей использования, см. статью [ведение журнала за использование и стоимость данных](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Какие состояния контейнеров указаны в таблице Контаинеринвентори?

Таблица ContainerInventory содержит сведения об остановленных и запущенных контейнерах. Эта таблица заполняется рабочим процессом агента, который получает из docker полный список контейнеров (работающих и остановленных) и пересылает эти данные в рабочую область Log Analytics.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Разделы справки устранить ошибку *регистрации отсутствующей подписки* ?

Если вы получаете ошибку **регистрация подписки для Microsoft. OperationsManagement**, ее можно устранить, зарегистрировав поставщик ресурсов **Microsoft. OperationsManagement** в подписке, в которой определена Рабочая область. Документацию по этому процессу можно найти [здесь](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Поддерживается ли кластеры AKS, поддерживающие RBAC?

Решение для мониторинга контейнеров не поддерживает RBAC, но поддерживается с Azure Monitor для контейнеров. На странице сведений о решении может неправильно отображаться информация в колонках с данными о таких кластерах.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Как включить сбор журналов для контейнеров в пространстве имен kube-system через Helm?

Сбор журналов из контейнеров в пространстве имен kube-system по умолчанию отключен. Вы можете включить сбор журналов, задав переменную среды для агента OMS. Дополнительные сведения см. на странице [Azure Monitor for Containers](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Как обновить агент OMS до последней выпущенной версии?

Сведения об обновлении агента, см. в статье [Как управлять агентом Azure Monitor для контейнеров](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Как включить многострочное ведение журнала?

В настоящее время Azure Monitor для контейнеров не поддерживает многострочное ведение журнала, но вы можете применить обходные пути. Например, настройте вывод данных из всех служб в формате JSON, и тогда Docker/Moby будут сохранять эти данные в одну строку.

Следующий пример демонстрирует сохранение журнала в формате объекта JSON для тестового приложения node.js:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Эти данные будут выглядеть, как в следующем примере, в Azure Monitor для журналов при запросе.

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Чтобы получить подробный обзор проблемы, просмотрите следующую [ссылку GitHub](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Разделы справки устранить ошибки Azure AD при включении Live Logs? 

Может появиться следующее сообщение об ошибке: **URL-адрес ответа, указанный в запросе, не совпадает с URL-адреса ответа, настроенного для приложения: "< приложение с идентификатором\>"** . Решение для решения этой проблемы можно найти в статье [как просматривать данные контейнера в режиме реального времени с помощью Azure Monitor для контейнеров](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Почему не удается обновить кластер после адаптации?

Если после включения Azure Monitor для контейнеров в кластере AKS вы удалите Log Analytics рабочую область, в которую кластер отправит свои данные, при попытке обновления кластера произойдет сбой. Чтобы обойти это, необходимо отключить мониторинг, а затем снова включить его, указав другую действительную рабочую область в подписке. При повторном выполнении обновления кластера его необходимо обработать и завершить успешно.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Какие порты и домены нужно открыть или список разрешений для агента?

См. [требования к сетевому брандмауэру](insights/container-insights-onboard.md#network-firewall-requirements) для сведений о конфигурации прокси-сервера и брандмауэра, необходимые для контейнерного агента с помощью облаков Azure, Azure для государственных организаций США и Azure для Китая (21vianet).

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor для виртуальных машин (Предварительная версия)
В этом списке вопросов и ответов от Майкрософт приведены часто задаваемые вопросы об Azure Monitor для виртуальных машин. Если у вас есть другие вопросы об этом решении, вы можете задать их на [форуме для обсуждений](https://feedback.azure.com/forums/34192--general-feedback). Если вопрос задается часто, мы добавим его в эту статью, чтобы его можно было найти быстро и легко.

### <a name="can-i-onboard-to-an-existing-workspace"></a>Можно ли подключиться к существующей рабочей области?
Если ваши виртуальные машины уже подключены к рабочей области Log Analytics, вы можете продолжить использовать эту рабочую область при подключении к Azure Monitor для виртуальных машин при условии, что эта рабочая область находится в одном из перечисленных [здесь](insights/vminsights-enable-overview.md#prerequisites) поддерживаемых регионов.

При подключении мы настраиваем счетчики производительности для рабочей области. В результате система начинает собирать отчетные данные обо всех виртуальных машинах в рабочей области, а затем отображает и анализирует их в Azure Monitor для виртуальных машин.  В результате будут отображаться данные производительности, которые получены со всех виртуальных машин, подключенных к выбранной рабочей области.  Функции "Работоспособность" и "Схема" будут включены только для тех виртуальных машин, которые вы укажете для подключения.

Дополнительные сведения о включенных счетчиках производительности см. в нашей [обзорной статье о включении](insights/vminsights-enable-overview.md#performance-counters-enabled) .

### <a name="can-i-onboard-to-a-new-workspace"></a>Можно ли подключиться к новой рабочей области? 
Если на данный момент ваши виртуальные машины не подключены к существующей рабочей области Log Analytics, вам потребуется создать рабочую область для хранения ваших данных. Если вы настроите одну виртуальную машину Azure для Azure Monitor для виртуальных машин на портале Azure, будет автоматически создана рабочая область, используемая по умолчанию.

Если вы решили использовать метод на основе сценария, эти действия описаны в статье [включение Azure Monitor для виртуальных машин (Предварительная версия) с использованием Azure PowerShell или диспетчер ресурсов шаблона](insights/vminsights-enable-at-scale-powershell.md) . 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Что делать, если виртуальная машина уже отправляет отчеты в существующую рабочую область?
Если вы уже собираете данные со своих виртуальных машин, возможно, вы уже настроили их для передачи отчетных данных в существующую рабочую область Log Analytics.  Пока эта рабочая область находится в одном из поддерживаемых регионов, вы можете включить Azure Monitor для виртуальных машин для этой существующей рабочей области.  Если рабочая область, которую вы уже используете, не находится в одном из поддерживаемых нами регионов, то на данный момент вам не удастся подключиться к Azure Monitor для виртуальных машин.  Мы активно работаем над поддержкой дополнительных регионов.

>[!NOTE]
>Мы настраиваем счетчики производительности для рабочей области, которые затрагивают все виртуальные машины, отправляющие отчетные данные в эту рабочую область, независимо от того, подключите ли вы их к Azure Monitor для виртуальных машин. Дополнительные сведения о настройке счетчиков производительности для рабочей области см. в нашей [документации](platform/data-sources-performance-counters.md). Сведения о счетчиках, настроенных для Azure Monitor для виртуальных машин, см. в статье [включение Azure Monitor для виртуальных машин](insights/vminsights-enable-overview.md#performance-counters-enabled) .  

### <a name="why-did-my-vm-fail-to-onboard"></a>Почему не удалось подключить мою виртуальную машину?
При подключении виртуальной машины Azure на портале Azure выполняются указанные ниже действия.

* Система создает рабочую область Log Analytics, используемую по умолчанию, если был выбран соответствующий параметр.
* Система настраивает счетчики производительности для выбранной рабочей области. Если не удастся выполнить это действие, вы заметите, что в некоторых диаграммах и таблицах производительности не отображаются данные для подключенной вами виртуальной машины. Это можно исправить, только выполнив скрипт PowerShell, описанный [здесь](insights/vminsights-enable-at-scale-powershell.md#enable-performance-counters).
* Если система определяет, что необходим агент Log Analytics, она устанавливает его на виртуальных машинах Azure, используя расширение виртуальной машины.  
* При необходимости на виртуальных машинах Azure с использованием расширения устанавливается программа Dependency Agent. Ее требует функция "Схема" в Azure Monitor для виртуальных машин.  
* При необходимости настраиваются компоненты Azure Monitor, поддерживающие функцию "Работоспособность". Кроме того, настраивается виртуальная машина так, чтобы она предоставляла данные о работоспособности.

В процессе подключения мы проверяем состояние на каждом из указанных выше элементов, чтобы возвращать сведения о состоянии уведомления вам на портале. Настройка рабочей области и установка агента обычно занимает 5–10 минут. Просмотр данных мониторинга и работоспособности на портале занимает еще 5–10 минут.  

Если вы инициировали подключение, но после этого отображаются сообщения о том, что необходимо подключить виртуальную машину, подождите около 30 минут, пока виртуальная машина завершит процесс. 

### <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>После включения решения "Azure Monitor для виртуальных машин" я вижу, что функция работоспособности отслеживает все мои виртуальные машины. Почему?
Функция работоспособности включена для всех виртуальных машин, которые подключены к рабочей области Log Analytics, даже если это действие запускалось с отдельной виртуальной машины.

### <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Могу ли я изменить график, по которому критерии работоспособности будут оценивать состояние?
Нет, в этом выпуске невозможно изменить период времени и частоту проверки работоспособности. 

### <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Могу ли я отключить критерии работоспособности для состояния, которое мне не нужно контролировать?
Критерии работоспособности нельзя отключить в этом выпуске.

### <a name="are-the-health-alert-severities-configurable"></a>Настраивается ли степень серьезности оповещения о работоспособности?  
Степень серьезности оповещения о работоспособности нельзя изменить. Его можно только включить или отключить. Кроме того, некоторые степени серьезности оповещения обновляются на основе состояния критериев работоспособности. 

### <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Если перенастроить параметры определенного критерия работоспособности, можно ли его применить к конкретному экземпляру?  
При изменении любого параметра экземпляра критерия работоспособности изменяются все экземпляры критериев работоспособности того же типа на виртуальной машине Azure. Например, если изменяется пороговое значение экземпляра критерия работоспособности диска, соответствующего логическому диску C, этот порог будет применяться ко всем другим логическим дискам, обнаруженным и контролируемым для этой же виртуальной машины.

### <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Контролирует ли функция работоспособности логические процессоры и ядра?
Нет, критерии работоспособности отдельного процессора и уровня логического процессора для Windows не включены. По умолчанию отслеживается только общий процент использования ЦП, чтобы эффективно оценить нагрузку на ЦП на основе общего количества логических ЦП, доступных для виртуальной машины Azure. 

### <a name="are-all-health-criteria-thresholds-configurable"></a>Все ли пороговые значения критериев работоспособности настраиваются?  
Пороговые значения для критериев работоспособности виртуальной машины Windows невозможно изменить, так как для работоспособности виртуальной машины настроено состояние *Выполняется* или *Доступно*. При запросе состояния работоспособности из [API мониторинга рабочей нагрузки](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components) отображается параметр *comparisonOperator* со значением **LessThan** или **GreaterThan** и с *пороговым* значением **4** для службы или объекта при таких ошибках:
   - Служба "Работоспособность служб" DNS-клиента не запущена. 
   - Служба "Работоспособность служб" DHCP-клиента не запущена. 
   - Служба "Работоспособность служб" RPC не запущена. 
   - Служба "Работоспособность служб" брандмауэра Windows не запущена.
   - Служба "Работоспособность служб" журнала событий Windows не запущена. 
   - Служба "Работоспособность служб" сервера не запущена. 
   - Служба "Работоспособность служб" удаленного управления Windows не запущена. 
   - Ошибка или повреждение файловой системы — логический диск недоступен.

Пороговые значения для следующих критериев работоспособности Linux невозможно изменить, так как для их состояния работоспособности уже установлено значение *true*. Для состояния работоспособности отображается параметр *compareOperator* со значением **LessThan** и *пороговым* значением **1** при запросе из API мониторинга рабочей нагрузки для объекта в зависимости от его контекста:
   - Состояние логического диска — логический диск работает в автономном режиме или недоступен.
   - Состояние диска — диск работает в автономном режиме или недоступен.
   - Состояние сетевого адаптера — сетевой адаптер отключен.

### <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Как изменить оповещения, включенные в функцию работоспособности?
Правила генерации оповещений, определенные для каждого критерия работоспособности, не отображаются на портале Azure. Вы можете включить или отключить правило генерации оповещений о работоспособности только в [API мониторинга рабочей нагрузки](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). На портале Azure нельзя также присвоить [группы действий Azure Monitor](platform/action-groups.md) для оповещений о работоспособности. Можно использовать API параметров уведомлений для настройки группы действий, которая будет запускаться всякий раз, когда выдается оповещение о работоспособности. Группы действий сейчас можно назначать виртуальной машине, чтобы все *оповещения о работоспособности* для этой виртуальной машины активировали одни и те же группы действий. В отличие от традиционных оповещений Azure, для каждого правила генерации оповещений о работоспособности не предусмотрены отдельные группы действий. Кроме того, при активации оповещений о работоспособности поддерживаются только группы действий, настроенные для уведомления по электронной почте или с помощью SMS. 

### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>На диаграммах производительности не отображаются некоторые или все данные для моей виртуальной машины
Если данные производительности не отображаются в таблице дисков или в некоторых диаграммах производительности, возможно, не настроены счетчики производительности в рабочей области. Чтобы решить эту проблему, выполните указанный ниже [скрипт PowerShell](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell).

### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Чем функция "Схема" в Azure Monitor для виртуальных машин отличается от решения "Сопоставление служб"?
Функция "Схема" в Azure Monitor для виртуальных машин основана на решении "Сопоставление служб", но при этом имеет указанные ниже отличия.

* Доступ к представлению "Схема" можно получить, выбрав колонку "Виртуальная машина" или элемент "Azure Monitor для виртуальных машин" в Azure Monitor.
* Теперь в функции "Схема" можно щелкать подключения и отображать представления с данными метрик подключений на боковой панели для выбранного подключения.
* Появился новый API, который используется для создания схем с целью улучшения поддержки сложных схем.
* Отслеживаемые виртуальные машины теперь включены в узел групп клиентов, а на кольцевой диаграмме показаны доли отслеживаемых и неотслеживаемых виртуальных машин в группе.  Теперь ее также можно использовать для фильтрации списка компьютеров при развертывании группы.
* Отслеживаемые виртуальные машины теперь включены в узлы групп портов серверов, а на кольцевой диаграмме показаны доли отслеживаемых и неотслеживаемых машин в группе.  Теперь ее также можно использовать для фильтрации списка компьютеров при развертывании группы.
* Стиль схем был обновлен, и теперь он больше соответствует схеме приложений в Application Insights.
* Боковые панели обновлены и не имеют полного набора интеграции, поддерживаемого в Сопоставление служб Управление обновлениями, Отслеживание изменений, безопасности и службы поддержки. 
* Параметр для выбора групп и машин, которые необходимо сопоставить, был обновлен, и теперь поддерживает "Подписки", "Группы ресурсов", масштабируемые наборы виртуальных машин Azure и облачные службы.
* Вам не удастся создать группы компьютеров в решении "Сопоставление служб", используя функцию "Схема" в Azure Monitor для виртуальных машин.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Почему диаграммы производительности отображаются пунктирными линиями?
Это может произойти по нескольким причинам.  В случаях, когда есть пробелы в собранных данных, мы отображаем строки пунктирной линией.  Если вы изменили частоту выборки данных для включенных счетчиков производительности (по умолчанию сбор данных выполняется каждые 60 секунд), на диаграмме могут отображаться пунктирные линии. Это происходит, если вы выбрали малый диапазон времени для диаграммы, а частота выборки меньше, чем размер сегмента, используемого в диаграмме (например, частота выборки составляет один раз в 10 минут, а каждый сегмент на диаграмме соответствует 5 минутам).  В этом случае, если вы выберете больший диапазон времени для отображения, линии диаграммы должны вновь стать сплошными.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Поддерживаются ли группы в Azure Monitor для виртуальных машин?
Да, после установки агента зависимостей мы собираем сведения с виртуальных машин для отображения групп в зависимости от подписки, группы ресурсов, масштабируемых наборов виртуальных машин и облачных служб.  Если вы используете Сопоставление служб и создали группы компьютеров, они также отображаются.  Группы компьютеров также появятся в фильтре групп, если они были созданы для просматриваемой рабочей области. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Как отобразить сведения о факторах, влияющих на линию 95-го процентиля на агрегированных диаграммах производительности?
По умолчанию список отсортирован так, что в нем отображаются виртуальные машины с самым большим значением для 95-го процентиля для выбранной метрики. Исключение составляет диаграмма "Доступная память", на которой показаны компьютеры с самым маленьким значением 5-го процентиля.  Если щелкнуть диаграмму, откроется представление со списком компьютеров с **лучшими параметрами**.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Как функция "Схема" обрабатывает дублирующиеся IP-адреса в разных виртуальных сетях и подсетях?
Если диапазоны IP-адресов дублируются при использовании виртуальных машин либо масштабируемых наборов виртуальных машин Azure в подсетях и виртуальных сетях, в функции "Схема" Azure Monitor для виртуальных машин может отображаться неправильная информация. Это известная проблема, и мы работаем над ее решением.

### <a name="does-map-feature-support-ipv6"></a>Поддерживает ли функция "Схема" протокол IPv6?
На данный момент функция "Схема" поддерживает только протокол IPv4, но мы работаем над поддержкой протокола IPv6. Мы также поддерживаем протокол IPv4, туннелируемый через протокол IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Когда я загружаю схему для группы ресурсов или другой большой группы, становится трудно просматривать схему
Несмотря на то что мы усовершенствовали функцию "Схема" в части отображения больших и сложных конфигураций, мы понимаем, что на схеме может быть много узлов, подключений и узлов, работающих как кластеры.  Мы намерены продолжать работу над улучшением функции масштабирования.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Почему внешний вид диаграммы сети на вкладке "Производительность" отличается от внешнего вида диаграммы сети на странице общих сведений о виртуальной машине Azure?

На странице общих сведений о виртуальной машине Azure отображаются диаграммы, основанные на измерениях активности гостевой виртуальной машины, выполняемых узлом.  На диаграмме сети на странице общих сведений о виртуальной машине Azure отображается только сетевой трафик, за который будет выставлен счет.  Это не включает трафик между виртуальными сетями.  Данные и диаграммы, отображаемые для Azure Monitor для виртуальных машин, основаны на данных из гостевой виртуальной машины, и в сетевой диаграмме отображается весь входящий и исходящий трафик TCP/IP для этой виртуальной машины, включая взаимную виртуальную сеть.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Как измеряется время отклика для данных, хранящихся в Вмконнектион и отображаемых на панели подключения и в книгах?

Время отклика является приближением. Поскольку мы не инструментирован код приложения, мы не знаем, когда начинается запрос и когда он поступает. Вместо этого мы отслеживаем данные, отправляемые по подключению, а затем получаем данные через это подключение. Наш агент отслеживает эти операции отправки и получения и пытается связать их: последовательность операций отправки, за которыми следует последовательность Receive, интерпретируется как пара "запрос-ответ". Интервал между этими операциями — время ответа. Он будет включать задержку сети и время обработки сервера.

Это приближение хорошо работает для протоколов, основанных на запросах и ответах: один запрос выходит за пределы соединения и достигает одного ответа. Это происходит в случае с HTTP (S) (без конвейера), но не для других протоколов.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Есть ли ограничения для Log Analytics ценовой категории "Бесплатный"?
Если вы настроили Azure Monitor с рабочей областью Log Analytics в ценовой категории *Бесплатный*, функция сопоставления Azure Monitor для виртуальных машин будет поддерживать подключение только пяти машин к рабочей области. Если к бесплатной рабочей области подключено пять виртуальных машин, после отключения одной виртуальной машины и последующего подключения новой виртуальной машины эта новая виртуальная машина не будет отслеживаться и отображаться на странице сопоставления.  

В этом случае отобразится предложение **Попробовать сейчас** при открытии виртуальной машины и выборе функции **Аналитические сведения (предварительная версия)** в левой области, даже если эта функция ранее была установлена на виртуальной машине.  Предложение не отобразится, если эта виртуальная машина не подключена к Azure Monitor для виртуальных машин. 


## <a name="next-steps"></a>Следующие шаги
Если вы не ответили на этот вопрос, вы можете обратиться к следующим форумам, чтобы получить дополнительные вопросы и ответы.

- [Служба Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Общие отзывы о Azure Monitor см. на [форуме обратной связи](https://feedback.azure.com/forums/34192--general-feedback).
