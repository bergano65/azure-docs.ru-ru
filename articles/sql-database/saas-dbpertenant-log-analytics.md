---
title: Использование журналов Azure Monitor с многоклиентским приложением базы данных SQL
description: Настройка и использование журналов Azure Monitor в приложении SaaS для базы данных SQL Azure с клиентами
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: d7b568424d5e33c19efc9d9d9c21d0023459b6c7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692148"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Настройка и использование журналов Azure Monitor с помощью приложения SaaS для базы данных SQL

В этом руководстве описано, как настроить и использовать [журналы Azure Monitor](/azure/log-analytics/log-analytics-overview) для мониторинга эластичных пулов и баз данных. Данный документ основан на руководстве [Мониторинг производительности баз данных и пулов SQL Azure в мультитенантном приложении SaaS и управление ею](saas-dbpertenant-performance-monitoring.md). В нем показано, как использовать журналы Azure Monitor для расширения мониторинга и оповещений, предоставленных в портал Azure. Журналы Azure Monitor поддерживают мониторинг тысяч эластичных пулов и сотен тысяч баз данных. Журналы Azure Monitor предоставляют единое решение для мониторинга, которое может интегрировать мониторинг различных приложений и служб Azure в нескольких подписках Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Установите и настройте журналы Azure Monitor.
> * Используйте журналы Azure Monitor для мониторинга пулов и баз данных.

Для работы с этим руководством выполните следующие предварительные требования:

* Должно быть развернуто приложение SaaS Wingtip Tickets, использующее одну базу данных на клиент. Изучите инструкции из руководства по быстрому [развертыванию приложения SaaS Wingtip Tickets, использующего одну базу данных на клиент](saas-dbpertenant-get-started-deploy.md).
* Установите Azure PowerShell. Дополнительные сведения см. в статье [Начало работы с Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Обсуждение сценариев и шаблонов SaaS и их влияния на требования к решениям для мониторинга можно найти в статье [Мониторинг производительности баз данных и пулов SQL Azure в мультитенантном приложении SaaS и управление ею](saas-dbpertenant-performance-monitoring.md).

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Мониторинг производительности баз данных и эластичных пулов и управление ими с помощью журналов Azure Monitor

Для Базы данных SQL средства мониторинга и создания оповещений для баз данных и пулов доступны на портале Azure. Эти встроенные функции мониторинга и оповещения удобны, но зависят от ресурсов. Это означает, что они не так хорошо подходят для отслеживания крупных систем или предоставления унифицированного представления ресурсов и подписок.

Для сценариев с большими объемами событий можно использовать журналы Azure Monitor для мониторинга и оповещений. Azure Monitor — это отдельная служба Azure, которая позволяет выполнять аналитику по журналам диагностики и телеметрии, собранным в рабочей области из потенциально многих служб. Журналы Azure Monitor предоставляют встроенный язык запросов и средства визуализации данных, позволяющие выполнять анализ рабочих данных. Решение "Аналитика SQL Azure" предоставляет несколько предопределенных представлений и запросов мониторинга и оповещений эластичных пулов и баз данных. Журналы Azure Monitor также предоставляют пользовательский конструктор представлений.

Рабочие области OMS теперь называются рабочими областями Log Analytics. Решения на основе аналитики и рабочих областей Log Analytics можно запустить на портале Azure. Портал Azure является более новой точкой доступа, однако может уступать порталу Operations Management Suite в некоторых областях.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Создание диагностических данных о производительности путем имитации рабочей нагрузки в клиентах 

1. В интегрированной среде сценариев PowerShell откройте файл *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Demo-PerformanceMonitoringAndManagement.ps1*. Не закрывайте этот сценарий, так как при работе с данным руководством может потребоваться запустить несколько сценариев генератора нагрузки.
1. Если вы еще не сделали этого, подготовьте пакет клиентов, чтобы предоставить более содержательный контекст мониторинга. Этот процесс займет несколько минут.

   а. Чтобы выбрать сценарий **подготовки пакета клиентов**, задайте для параметра _$DemoScenario_ значение 1.

   b. Чтобы запустить сценарий или развернуть 17 дополнительных клиентов, нажмите клавишу F5.

1. Теперь запустите генератор нагрузки, чтобы выполнить имитацию нагрузки на всех клиентах.

    а. Установите **$DemoScenario = 2**, _Создайте нормальную нагрузку с интенсивностью (примерно 30 DTU)_ .

    b. Нажмите клавишу F5 для запуска скрипта.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Получение скриптов для SaaS-приложения Wingtip Tickets c базой данных для каждого клиента

Сценарии и исходный код приложения SaaS Wingtip Tickets мультитенантной базы данных размещены в репозитории GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Инструкции по скачиванию и разблокированию сценариев PowerShell приложения Wingtip Tickets приведены [здесь](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Установка и Настройка рабочей области Log Analytics и Аналитика SQL Azure решения

Azure Monitor — это отдельная служба, которая должна быть настроена. Журналы Azure Monitor собирает данные журнала, телеметрию и метрики в Log Analytics рабочей области. Рабочую область Log Analytics нужно создать, как и другие ресурсы в Azure. Рабочую область не обязательно создавать в одной группе ресурсов с приложением, которое она отслеживает. Однако зачастую это самый оптимальный вариант. Использование одной группы ресурсов с приложением Wingtip Tickets гарантирует, что рабочая область удаляется вместе с приложением.

1. В интегрированной среде сценариев PowerShell откройте файл *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. Нажмите клавишу F5 для запуска скрипта.

Теперь можно открыть журналы Azure Monitor в портал Azure. Для сбора данных телеметрии и их отображения в рабочей области Log Analytics требуется несколько минут. Чем дольше выполняется сбор системных данных диагностики, тем содержательнее результат. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Использование рабочей области Log Analytics и решения SQL Analytics для мониторинга пулов и баз данных


В этом упражнении откройте рабочую область Log Analytics в портал Azure, чтобы просмотреть данные телеметрии, собранные для баз данных и пулов.

1. Перейдите на [портал Azure](https://portal.azure.com). Выберите **все службы** , чтобы открыть рабочую область log Analytics. Найдите Log Analytics.

   ![Открыть рабочую область Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Выберите рабочую область _wtploganalytics-&lt;пользователь&gt;_ .

1. Выберите **Обзор** , чтобы открыть решение log analytics в портал Azure.

   ![Обзор](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Активация решения может занять несколько минут. 

1. Щелкните элемент **Аналитика SQL Azure**, чтобы открыть его.

    ![Элемент обзора](media/saas-dbpertenant-log-analytics/overview.png)

1. Используйте внутреннюю полосу прокрутки внизу, чтобы просмотреть страницы решений в представлении. При необходимости обновите страницу.

1. На странице сводки можно щелкнуть нужные элементы или отдельную базу данных, чтобы открыть обозреватель с детализированным данными.

    ![Панель мониторинга log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Измените параметр фильтра, чтобы указать диапазон времени. В рамках этого руководства выберите **Последний час**.

    ![Фильтр времени](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Выберите отдельную базу данных, чтобы просмотреть данные об использовании запросов и метрики для нее.

    ![Аналитика базы данных](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Чтобы просмотреть метрики использования, прокрутите страницу аналитики вправо.
 
     ![Метрики базы данных](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Прокрутите страницу аналитики влево и щелкните элемент сервера в списке **Сведения о ресурсе**.  

    ![Список "Сведения о ресурсе"](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Откроется страница со списком пулов и баз данных на сервере.

    ![Сервер с пулами и базами данных](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Выберите пул. На открывшейся странице пула прокрутите вправо, чтобы просмотреть метрики пула. 

    ![Метрики пула](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. В рабочей области Log Analytics выберите **Портал OMS**, чтобы открыть на нем рабочую область.

    ![Рабочая область Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

В рабочей области Log Analytics можно подробнее исследовать данные журнала и метрик. 

Мониторинг и оповещения в журналах Azure Monitor основаны на запросах к данным в рабочей области, в отличие от предупреждений, определенных для каждого ресурса в портал Azure. Создавая оповещения на основе запросов, вместо того чтобы определять одно оповещение для каждой базы данных, можно определить одно общее оповещение для всех баз данных. Запросы ограничиваются только данными, доступными в рабочей области.

Дополнительные сведения об использовании журналов Azure Monitor для запроса и настройки оповещений см. в разделе [Работа с правилами генерации оповещений в журналах Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Azure Monitor журналов для расходов на базу данных SQL в зависимости от объема данных в рабочей области. В этом руководстве вы создали бесплатную рабочую область с ограничением в 500 МБ в день. По достижении этого ограничения данные больше не будут добавляться в рабочую область.


## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Установите и настройте журналы Azure Monitor.
> * Используйте журналы Azure Monitor для мониторинга пулов и баз данных.

Ознакомьтесь со статьей [Настройка и использование Log Analytics (OMS) с мультитенантным приложением SaaS для базы данных SQL Azure](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Дополнительные руководства на основе приложения SaaS Wingtip Tickets, использующего одну базу данных на клиент](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Журналы Azure Monitor](../azure-monitor/insights/azure-sql.md)
