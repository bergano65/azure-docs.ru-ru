---
title: Использование мониторинга и ожидаемые затраты в Azure Monitor
description: Обзор процесса использования страницы использования и ожидаемых затрат в Azure Monitor
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 22c7243cd966f458610c2abc67ed5540b37357b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658821"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Использование мониторинга и ожидаемые затраты в Azure Monitor

> [!NOTE]
> В этой статье описывается, как просматривать использование и сметные затраты в нескольких функциях мониторинга Azure. Похожие статьи для конкретных компонентов Azure Monitor включают:
> - [Управление использованием и затратами с помощью журналов Azure Monitor](manage-cost-storage.md) описывает, как контролировать свои расходы, изменяя период хранения данных, а также как анализировать и предупреждать об использовании данных.
> - [Управление использованием и затратами для Application Insights](../../azure-monitor/app/pricing.md) описывает, как анализировать использование данных в Application Insights.

## <a name="azure-monitor-pricing-model"></a>Модель ценообразования Azure Monitor

Базовая модель выставления счетов Azure Monitor — это удобное для потребления ценообразование («Pay-As-You-Go»). Вы платите только за то, что используете. Сведения о ценах доступны для [оповещения, метрик, уведомлений,](https://azure.microsoft.com/pricing/details/monitor/) [аналитики журналов](https://azure.microsoft.com/pricing/details/log-analytics/) и [анализа приложений.](https://azure.microsoft.com/pricing/details/application-insights/) 

В дополнение к модели Pay-As-You-Go для данных журнала, Журнал Analytics имеет резервирование емкости, что позволяет сэкономить до 25% по сравнению с ценой Pay-As-You-Go. Цены на резервирование емкости позволяют купить бронирование, начиная со 100 ГБ/день. Любое использование выше уровня бронирования будет выставлен счет по тарифу Pay-As-You-Go. [Узнайте больше](https://azure.microsoft.com/pricing/details/monitor/) о ценах на резервирование емкости.

Некоторые клиенты будут иметь доступ к [устаревшим уровням ценообразования Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) и [устаревшему уровню ценообразования Enterprise Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier) 

## <a name="understanding-your-azure-monitor-costs"></a>Понимание затрат на мониторинг Azure

Существует два этапа для понимания затрат. Во-первых, при рассмотрении Azure Monitor в качестве решения для мониторинга. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Оценка затрат на управление окружающей средой

Если вы еще не используете журналы Azure Monitor, вы можете использовать [калькулятор цен Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) для оценки стоимости использования Azure Monitor. Начните с ввода "Azure Monitor" в поле поиска и нажатия на полученную плитку Azure Monitor. Прокрутите страницу до Azure Monitor и выберите один из вариантов из отбрасывания типа:

- Запросы метрик и оповещения  
- Log Analytics
- Application Insights

В каждом из них калькулятор ценообразования поможет вам оценить ваши вероятные затраты на основе ожидаемого использования.

Например, с помощью Log Analytics вы можете ввести количество вс-чатов и ГБ данных, которые вы ожидаете собрать от каждого VM. Обычно от 1 ГБ до 3 ГБ данных в месяц попадает из типичного VM Azure. Если вы уже оценили журналы Azure Monitor, вы можете использовать статистику данных из своей среды. Ниже приведены данные о том, как определить [количество контролируемых ВМ](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) и [объем данных, в которые попадает рабочее пространство.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)

Аналогичным образом для Application Insights, если вы включите функциональность "Оценка объема данных на основе активности приложения", вы можете предоставить данные о вашем приложении (запросы в месяц и просмотров страниц в месяц, в случае, если вы будете собирать телеметрию со стороны клиента), и затем калькулятор расскажет вам средний и 90-й процентиль объем данных, собранных аналогичными приложениями. Эти приложения охватывают диапазон конфигурации Application Insights (например, некоторые из них имеют выборку по умолчанию, некоторые из них не имеют выборки и т.д.), так что у вас все еще есть контроль, чтобы уменьшить объем данных, которые вы глотаете гораздо ниже медианного уровня с помощью выборки. Но это отправная точка, чтобы понять, что другие, подобные клиенты видят. [Подробнее](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) об оценке затрат на анализ приложений.

### <a name="understanding-your-usage-and-estimated-costs"></a>Понимание использования и сметных расходов

Важно понимать и отслеживать использование после использования Azure Monitor, и существует множество инструментов, облегчающих это. 

Azure предоставляет большую полезную функциональность в центре [управления затратами Azure и Биллинга.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) После открытия концентратора **управления затратами Azure и Биллинга** нажмите на **управление затратами** и выберите [область действия](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (набор ресурсов для исследования). 

Затем, чтобы просмотреть расходы Azure Monitor за последние 30 дней, нажмите на плитку **Ежедневные затраты,** выберите "Последние 30 дней" в соответствии с относительными датами и добавьте фильтр, который выбирает имена служб:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Аналитика

Это приводит к таким представлениям, как:

![Скриншот управления затратами Azure](./media/usage-estimated-costs/010.png)

Отсюда вы можете просверлить из этой накопленной сводки затрат, чтобы получить более мелкие детали в представлении "Cost by resource". В текущих уровнях ценообразования данные журнала Azure Log заряжаются на одном и том же наборе счетчиков, независимо от того, исходят ли они из Log Analytics или Application Insights. Чтобы отделить затраты от использования Log Analytics или Application Insights, можно добавить фильтр по **типу ресурса.** Чтобы просмотреть все затраты Наapplication, отфильтруйте тип ресурса на "microsoft.insights/components", а для оценки анализа журналов отфильтруйте тип ресурсов на "microsoft.operationalinsights/workspaces". 

Более подробная информация об использовании доступна при [загрузке использования с портала Azure.](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal) В загруженной таблице можно увидеть использование ресурса Azure в день. В этой таблице Excel использование ресурсов Application Insights можно найти, сначала отфильтровав в столбце "Категория метр", чтобы показать "Application Insights" и "Log Analytics", а затем добавить фильтр в столбце "Instance ID", который "содержит microsoft.insights/components".  Большинство использования Application Insights сообщается на счетчиках с meter Category of Log Analytics, так как для всех компонентов Azure Monitor имеется единый бэкэнд журналов.  Сообщается только о ресурсах Application Insights о устаревших уровнях ценообразования и многоступенчатых веб-тестах с помощью категории Meter of Application Insights.  Использование отображается в столбце "Потребление" и единица для каждой записи отображается в столбце "Единица измерения".  Более подробная информация доступна, чтобы помочь вам [понять ваш счет Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

> [!NOTE]
> Использование **управления затратами** в центре **управления затратами Azure и Биллинг** является предпочтительным подходом к широкому пониманию затрат на мониторинг.  Опыт **использования и сметных затрат** для анализа [журналов](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) и [анализа приложений](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) позволяет получить более глубокие сведения для каждой из этих частей Azure Monitor.

Другим вариантом просмотра использования Azure Monitor является страница **«Использование и сметные затраты»** в концентраторе монитора. Это показывает использование основных функций мониторинга, таких как [оповещения, метрики, уведомления,](https://azure.microsoft.com/pricing/details/monitor/) [аналитика журналов Azure](https://azure.microsoft.com/pricing/details/log-analytics/)и [Azure Application Insights.](https://azure.microsoft.com/pricing/details/application-insights/) Для клиентов с тарифными планами, доступными до апреля 2018 года, это также включает использование службы Log Analytics, приобретенной в соответствии с предложением "Анализ и аналитика".

На этой странице пользователи могут просматривать использование этих ресурсов за последние 31 день, выведенное для каждой подписки. `Drill-ins`показать тенденции использования в течение 31-дневного периода. Для этой оценки необходимо собрать много данных, поэтому наберитесь терпения при загрузке страницы.

Этот пример показывает мониторинг использования и оценку возникших затрат:

![Снимок экрана: страница использования и ожидаемых затрат на портале](./media/usage-estimated-costs/001.png)

Чтобы открыть диаграмму, которая показывает тенденции использования в течение последних 31 дня, выберите ссылку в столбце ежемесячного потребления. 

![Снимок экрана: "Включено на каждый узел линейчатой диаграммы"](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Права на подписку на программу Operations Management Suite

Клиенты, которые приобрели Microsoft Operations Management Suite E1 и E2, имеют право на назначение приема данных для [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) и [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Чтобы получить эти назначения для рабочих областей Log Analytics либо ресурсов Application Insights в конкретной подписке, необходимо следующее: 

- для рабочих областей Log Analytics должна использоваться ценовая категория "За узел (OMS)";
- Ресурсы Application Insights должны использовать уровень ценообразования "Предприятие".

В зависимости от количества узлов пакета, приобретенных вашей организацией, перемещение некоторых подписок в ценовой уровень Pay-As-You-Go (Per GB) может быть выгодным, но это требует тщательного рассмотрения.

> [!WARNING]
> Если в вашей организации есть текущие наборы управления операциями Майкрософт E1 и E2, то, как правило, лучше всего сохранить рабочие области log Analytics в ценовом уровне "Per-node (OMS) и ресурсы Application Insights в ценовом уровне "Предприятие". 
>
