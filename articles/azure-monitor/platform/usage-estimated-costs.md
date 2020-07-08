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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658821"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Использование мониторинга и ожидаемые затраты в Azure Monitor

> [!NOTE]
> В этой статье описывается, как просматривать использование и оценивать затраты в нескольких функциях мониторинга Azure. Связанные статьи по конкретным компонентам Azure Monitor включают:
> - [Управление использованием и затратами с помощью журналов Azure Monitor](manage-cost-storage.md) описывает управление затратами путем изменения срока хранения данных, а также анализ и оповещение об использовании данных.
> - [Управление использованием и затратами Application Insights](../../azure-monitor/app/pricing.md) описывает анализ использования данных в Application Insights.

## <a name="azure-monitor-pricing-model"></a>Модель ценообразования Azure Monitor

Базовая модель выставления счетов Azure Monitor — это ориентированное на облачное использование ценообразование (с оплатой по мере использования). Вы платите только за то, что используете. Сведения о ценах доступны для [оповещений, метрик, уведомлений](https://azure.microsoft.com/pricing/details/monitor/), [log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) и [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Помимо модели с оплатой по мере использования для данных журнала, Log Analytics содержит резервирование емкости, что позволяет сэкономить до 25% по сравнению с ценой оплаты по мере использования. Цены на резервирование мощности дают возможность приобрести резервирование, начиная со 100 ГБ в день. При превышении уровня использования резервирования будет взиматься плата по мере использования. Дополнительные [сведения](https://azure.microsoft.com/pricing/details/monitor/) о ценах на резервирование емкости.

У некоторых клиентов будет доступ к [устаревшим](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) ценовым категориям log Analytics и [устаревшей ценовой категории Enterprise Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier). 

## <a name="understanding-your-azure-monitor-costs"></a>Основные сведения о затратах на Azure Monitor

Существует два этапа для понимания затрат. Первый — при рассмотрении Azure Monitor в качестве решения для мониторинга. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Оценка затрат на управление средой

Если вы еще не используете журналы Azure Monitor, можно использовать [Калькулятор цен Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) , чтобы оценить стоимость использования Azure Monitor. Сначала введите Azure Monitor в поле поиска и щелкните отобразившуюся плитку Azure Monitor. Прокрутите страницу вниз, чтобы Azure Monitor, и выберите один из параметров в раскрывающемся списке Тип:

- Запросы метрик и оповещения  
- Log Analytics
- Application Insights

В каждом из этих средств калькулятор ценообразования поможет оценить вероятные затраты в зависимости от ожидаемого использования.

Например, с Log Analytics можно ввести количество виртуальных машин и ГБ данных, которые будут собираются из каждой виртуальной машины. Обычно от 1 ГБ до 3 ГБ данных месяц принимается из типичной виртуальной машины Azure. Если вы уже оцениваете журналы Azure Monitor, вы можете использовать статистику данных из собственной среды. Ниже приведены сведения о том, как определить [число отслеживаемых виртуальных машин](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) и [объем данных, принимаемых рабочей областью](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).

Аналогично Application Insights, если вы включили функцию "Оценка объема данных на основе действия приложения", вы можете предоставить входные данные о приложении (запросы в месяц и Просмотры страниц в месяц, если вы собираетесь собирать данные телеметрии на стороне клиента), а затем калькулятор сообщит, как медиана и 90 процентиль объем данных, собираемых аналогичными приложениями. Оценка выполняется по всему диапазону конфигураций Application Insights (например, для некоторых применяется выборка по умолчанию, а для других не применяется и т. д.), поэтому у вас всегда будет возможность, используя выборку, добиться объема заметно ниже среднего уровня. Но это даст вам отправную точку для понимания того, что получают другие клиенты с похожими задачами. Дополнительные [сведения](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) об оценке затрат на Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Понимание использования и предполагаемых затрат

Важно понимать и отменять использование с помощью Azure Monitor, и существует богатый набор средств для упрощения этого. 

В Azure доступно большое количество полезных функций в центре Azure [Управление затратами и выставление счетов](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json). После открытия центра **управления затратами Azure + центр выставления счетов** щелкните **Управление затратами** и выберите [область](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (набор ресурсов для изучения). 

Затем, чтобы просмотреть стоимость Azure Monitor за последние 30 дней, щелкните плитку **Ежедневные затраты** , выберите "последние 30 дней" в разделе относительные даты и добавьте фильтр, выбирающий имена служб:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Аналитика

Результатом является следующее представление:

![Снимок экрана службы управления затратами Azure](./media/usage-estimated-costs/010.png)

Отсюда вы можете получить подробную информацию из этой накопленной сводки по затратам для получения подробных сведений в представлении "стоимость по ресурсам". В текущих ценовых категориях данные журнала Azure выставляются по одному и тому же набору счетчиков независимо от того, откуда они берутся: Log Analytics или Application Insights. Чтобы отделить затраты от Log Analytics или Application Insights, можно добавить фильтр по **типу ресурса**. Чтобы просмотреть все Application Insights расходы, отфильтруйте тип ресурса на "Microsoft. Insights/Components", а для Log Analytics затрат, отфильтруйте тип ресурса "Microsoft. operationalinsights/workspaces". 

Дополнительные сведения об использовании можно получить, [загрузив сведения об использовании из портал Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). В скачанной электронной таблице вы можете просмотреть сведения об использовании каждого ресурса Azure в день. В этой таблице Excel данные об использовании, связанные с ресурсами Application Insights, можно найти с помощью следующих фильтров: для столбца "Категория учета" нужно выбрать "Application Insights" и "Log Analytics", а для столбца "Идентификатор экземпляра" — значение "содержит microsoft.insights/components".  Большая часть данных об использовании для Application Insights отображается по счетчикам в категории "Счетчик" Log Analytics, так как для всех компонентов Azure Monitor используется общая серверная часть сбора журналов.  В категорию учета Application Insights включаются только ресурсы Application Insights с устаревшими ценовыми категориями и многошаговые веб-тесты.  Данные об использовании отображаются в столбце "Использованное количество", а единица измерения для каждой записи отображается в столбце "Единица измерения".  Дополнительные сведения о счете за использование Microsoft Azure см. в [этой статье](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

> [!NOTE]
> Использование службы **управления затратами** в центре **управления затратами Azure и центром выставления счетов** является предпочтительным подходом к широкому пониманию затрат на мониторинг.  **Использование и оценка затрат** на [log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) и [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) предоставляют более подробные сведения о каждой из этих частей Azure Monitor.

Другим вариантом просмотра Azure Monitor использования является страница " **использование и оценка затрат** " в центре мониторинга. Здесь показано использование основных функций мониторинга, таких как [оповещения, метрики, уведомления](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)и [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Для клиентов с тарифными планами, доступными до апреля 2018 года, это также включает использование службы Log Analytics, приобретенной в соответствии с предложением "Анализ и аналитика".

На этой странице пользователи могут просматривать использование этих ресурсов за последние 31 день, выведенное для каждой подписки. `Drill-ins`Отображение тенденций использования в течение 31-дневного периода. Для этой оценки необходимо собрать много данных, поэтому наберитесь терпения при загрузке страницы.

Этот пример показывает мониторинг использования и оценку возникших затрат:

![Снимок экрана: страница использования и ожидаемых затрат на портале](./media/usage-estimated-costs/001.png)

Чтобы открыть диаграмму, которая показывает тенденции использования в течение последних 31 дня, выберите ссылку в столбце ежемесячного потребления. 

![Снимок экрана: "Включено на каждый узел линейчатой диаграммы"](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Права подписки Operations Management Suite

Клиенты, которые приобрели Microsoft Operations Management Suite E1 и E2, имеют право на назначение приема данных для [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) и [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Чтобы получить эти назначения для рабочих областей Log Analytics либо ресурсов Application Insights в конкретной подписке, необходимо следующее: 

- для рабочих областей Log Analytics должна использоваться ценовая категория "За узел (OMS)";
- Application Insights ресурсам следует использовать ценовую категорию "Корпоративный".

В зависимости от количества узлов в наборе, приобретенных организацией, перемещение некоторых подписок в ценовую категорию с оплатой по мере использования (в ГБ) может быть выгодным, но это требует тщательного рассмотрения.

> [!WARNING]
> Если в вашей организации используется текущая Microsoft Operations Management Suite E1 и E2, лучше всего не допустить Log Analytics рабочих областей в ценовой категории "на узел (OMS)" и Application Insightsных ресурсах в ценовой категории "Корпоративный". 
>
