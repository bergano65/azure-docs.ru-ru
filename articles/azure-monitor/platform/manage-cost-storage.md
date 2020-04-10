---
title: Управление использованием и затратами для журналов мониторинга Azure Документы Майкрософт
description: Узнайте, как изменить план ценообразования и управлять политикой объема и хранения данных для рабочего пространства журнала Analytics в Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: d03b053f2aa5de4a6f7874dbf4e6ccb3a305a964
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992085"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Управление использованием и затратами с помощью журналов Azure Monitor

> [!NOTE]
> В этой статье описывается, как понять и контролировать затраты на журналы Azure Monitor. В соответствующей статье [«Мониторинг использования и сметных затрат»](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) описывается, как просматривать использование и сметные затраты в нескольких функциях мониторинга Azure для различных моделей ценообразования. Все цены и затраты, указанные в этой статье, являются только целями. 

Журналы Azure Monitor предназначены для масштабирования и поддержки сбора, индексации и хранения огромных объемов данных в день из любого источника вашего предприятия или развернутых в Azure.  Хотя это может быть определяющим фактором для вашей организации, в конечном счете основной является экономическая эффективность. С этой целью важно понимать, что стоимость рабочего пространства Log Analytics не зависит только от объема собранных данных, а также зависит от выбранного плана и того, как долго вы решили хранить данные, генерируемые из подключенных источников.  

В этой статье мы рассмотрим, как можно упреждающе отслеживать рост объема и хранения данных, а также определяем пределы для контроля за этими затратами. 

## <a name="pricing-model"></a>Модель ценообразования

Цены по умолчанию для Log Analytics — это модель **Pay-As-You-Go,** основанная на объеме данных, втиснутых и опционально для более длительного хранения данных. Объем данных измеряется как размер данных, которые будут храниться. Каждое рабочее пространство Log Analytics заряжается как отдельная служба и вносит свой вклад в выставление счета за подписку Azure. Объем проглатывания данных может быть значительным в зависимости от следующих факторов: 

  - Количество включенных управленческих решений и их конфигурация (например. 
  - Количество контролируемых ВМ
  - Тип данных, собранных с каждого контролируемого VM 
  
В дополнение к модели Pay-As-You-Go, Log Analytics имеет уровни **резервирования емкости,** которые позволяют сэкономить до 25% по сравнению с ценой Pay-As-You-Go. Цены на резервирование емкости позволяют купить бронирование, начиная со 100 ГБ/день. Любое использование выше уровня бронирования будет выставлен счет по тарифу Pay-As-You-Go. Уровни резервирования емкости имеют 31-дневный период обязательств. В течение периода обязательств вы можете перейти на более высокий уровень резервирования емкости (который перезапустит 31-дневный период обязательств), но вы не можете вернуться к Pay-As-You-Go или к более низкому уровню резервирования емкости до окончания периода обязательств. 
[Узнайте больше](https://azure.microsoft.com/pricing/details/monitor/) о ценах на log Analytics Pay-As-You-Go и резервировании емкости. 

Во всех уровнях ценообразования объем данных рассчитывается на основе строкового представления данных по мере их готовности к хранению. Несколько [свойств, общих для всех типов данных,](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) `_ResourceId`не `_ItemId` `_IsBillable` включены в расчет размера события, в том числе, и `_BilledSize`.

Также обратите внимание, что некоторые решения, такие как [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) и [Azure Sentinel,](https://azure.microsoft.com/pricing/details/azure-sentinel/)имеют свою собственную модель ценообразования. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Оценка затрат на управление окружающей средой 

Если вы еще не используете журналы Azure Monitor, вы можете использовать [калькулятор цен Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) для оценки стоимости использования журналов. Начните с ввода "Azure Monitor" в поле поиска и нажатия на полученную плитку Azure Monitor. Прокрутите страницу до Azure Monitor и выберите аналитику журналов из системы отбрасывания типа.  Здесь вы можете ввести количество VMs и ГБ данных, которые вы ожидаете собрать от каждого VM. Обычно от 1 до 3 ГБ данных в месяц попадает из типичного VM Azure. Если вы уже оценили журналы Azure Monitor, вы можете использовать статистику данных из своей среды. Ниже приведены данные о том, как определить [количество контролируемых ВМ](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) и [объем данных, в которые попадает рабочее пространство.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume) 

## <a name="understand-your-usage-and-estimate-costs"></a>Понимание затрат на использование и оценка

Если вы используете журналы Azure Monitor, легко понять, какие затраты, вероятно, будут основаны на последних моделях использования. Для этого используйте **использование и сметные затраты на** использование журналов для анализа и анализа использования данных. Это показывает, сколько данных собирается каждым решением, сколько данных сохраняется и оценка ваших расходов на основе объема данных, поданных и любого дополнительного хранения за пределами включенной суммы.

![Использование и ожидаемые затраты](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Чтобы подробнее изучить данные, щелкните значок в верхней правой части любой диаграммы на странице **Использование и ожидаемые затраты**. Теперь вы можете доработать этот запрос, чтобы получить дополнительные сведения о потреблении.  

![Представление журналов](media/manage-cost-storage/logs.png)

На странице **Использование и оценка затрат** можно просмотреть сведения о томах данных за месяц. Сюда входят все данные, полученные и сохраненные в рабочей области Log Analytics.  Нажмите **сведения об использовании** в верхней части страницы, чтобы просмотреть панель мониторинга использования с информацией о тенденциях объема данных по источникам, компьютерам и предложениям. Чтобы просмотреть и установить ежедневное ограничение или изменить срок хранения, щелкните **Управление объемом данных**.
 
Оплата за использование Log Analytics добавляется в счет Azure. Дополнительную информацию о счете за подписку на Azure можно просмотреть в разделе выставления счетов портала Azure или на [Портале управления счетами и подписками Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Просмотр использования аналитики журналов на счете Azure 

Azure предоставляет большую полезную функциональность в центре [управления затратами Azure и Биллинга.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Например, функция «Анализ затрат» позволяет просматривать расходы ресурсов Azure. Добавление фильтра по типу ресурсов (к microsoft.operationalinsights/workspace for Log Analytics) позволит вам отслеживать ваши расходы.

Более глубокое понимание использования можно получить, [загрузив использование с портала Azure.](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal) В загруженной электронной таблице можно увидеть использование на ресурсе Azure (например, рабочее пространство log Analytics) в день. В этой таблице Excel использование рабочих областей Log Analytics можно найти, сначала отфильтровав в столбце "Категория метр", чтобы показать "Исследования и аналитики" (используется некоторыми устаревшими уровнями ценообразования) и "Лог-аналитика", а затем добавить фильтр на столбце "Instance ID", который "содержит рабочее пространство". Использование отображается в столбце "Потребление" и единица для каждой записи отображается в столбце "Единица измерения".  Более подробная информация доступна, чтобы помочь вам [понять ваш счет Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Изменение ценовой категории.

Чтобы изменить уровень ценообразования Log Analytics в рабочей области, 

1. На портале Azure откройте **использование и сметные затраты** из рабочего пространства, где вы увидите список каждого из уровней ценообразования, доступных для этого рабочего пространства.

2. Просмотрите сметные затраты для каждого из уровней ценообразования. Эта оценка основана на последних 31 днях использования, поэтому эта смета расходов зависит от того, что последние 31 день являются репрезентативными для вашего типичного использования. В приведенном ниже примере можно увидеть, как, основываясь на мокартинах данных за последние 31 день, это рабочее пространство будет стоить дешевле в уровне Pay-As-You-Go (#1) по сравнению с уровнем резервирования емкости в 100 ГБ/день (#2).  

    ![Ценовые категории](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. После рассмотрения сметных затрат на основе последних 31 дней использования, если вы решите изменить уровень ценообразования, нажмите **Выберите**.  

Вы также можете [установить уровень ценообразования через менеджер ресурсов Azure](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) с помощью `sku` параметра (в`pricingTier` шаблоне менеджера ресурсов Azure). 

## <a name="legacy-pricing-tiers"></a>Устаревшие ценовые категории

Подписки, которые имели рабочее пространство Log Analytics или ресурс Application Insights в нем до 2 апреля 2018 года, или связаны с корпоративным соглашением, которое началось до 1 февраля 2019 года, будет по-прежнему иметь доступ к использованию устаревших уровней ценообразования: **Free**, **Standalone (Per GB)** и **Per Node (OMS).**  Рабочие области в свободном уровне ценообразования будут иметь ежедневный проглатывание данных до 500 МБ (за исключением типов данных безопасности, собранных Центром безопасности Azure), а хранение данных ограничено 7 днями. Бесплатный уровень ценообразования предназначен только для целей оценки. Рабочие области в уровнях ценообразования Standalone или Per Node имеют настраиваемое удержание пользователей от 30 до 730 дней.

Плата за ценообразование Per Node за контролируемый VM (узло) на часовую детализацию. Для каждого контролируемого узла рабочее пространство выделяется 500 МБ данных в день, которые не выставляются. Это распределение агрегируется на уровне рабочей области. Данные, понижаемые выше совокупного ежедневного распределения данных, выставляются на ГБ как избытки данных. Обратите внимание, что в вашем счете, служба будет **Insight и Analytics** для использования журнала Analytics, если рабочее пространство находится в уровне ценообразования Per Node. 

> [!TIP]
> Если ваше рабочее пространство имеет доступ к уровню ценообразования **Per Node,** но вы задаетесь вопросом, будет ли оно стоить дешевле в уровне Pay-As-You-Go, вы можете [использовать запрос ниже,](#evaluating-the-legacy-per-node-pricing-tier) чтобы легко получить рекомендацию. 

Рабочие пространства, созданные до апреля 2016 года, также могут получить доступ к исходным уровням ценообразования **Standard** и **Premium,** которые зафиксировали хранение данных в 30 и 365 дней соответственно. Новые рабочие области не могут быть созданы в стандартных **или** **премиумовых** уровнях ценообразования, и если рабочее пространство перемещено из этих уровней, оно не может быть перемещено обратно. 

Более подробная информация об ограничениях ценового уровня доступна [здесь.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)

> [!NOTE]
> Чтобы использовать права, полученные при покупке подписки OMS E1, OMS E2 или настройки OMS для System Center, выберите ценовой уровень Log Analytics *за узле*.

## <a name="change-the-data-retention-period"></a>Изменение срока хранения данных

В следующих шагах описана настройка периода хранения данных журнала в рабочей области. Хранение данных может быть настроено от 30 до 730 дней (2 года) для всех рабочих областей, если они не используют устаревший уровень ценообразования Free. 

### <a name="default-retention"></a>Удержание по умолчанию

Чтобы установить удержание по умолчанию для рабочего пространства, 
 
1. На портале Azure из рабочего пространства выберите **использование и сметные затраты** из левой панели.
2. В верхней части страницы **Usage and estimated costs** (Использование и ожидаемые затраты) щелкните **Управление объемом данных**.
3. В области воспользуйтесь ползунком, чтобы увеличить или уменьшить количество дней, а затем нажмите кнопку **ОК**.  Если вы используете уровень *Бесплатный*, то вы не сможете изменить срок хранения данных. Необходимо перейти на платный уровень, чтобы управлять этим параметром.

    ![Изменение параметра хранения данных рабочей области](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
Сохранение также может быть [установлено через менеджер ресурсов Azure](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) с помощью `retentionInDays` параметра. Кроме того, если установить сохранение данных до 30 дней, можно вызвать немедленную чистку старых данных с помощью `immediatePurgeDataOn30Days` параметра, что может быть полезно для сценариев, связанных с соответствием. Эта функция разоблачается только через менеджер ресурсов Azure. 

Два типа данных - `Usage` и `AzureActivity` - сохраняются в течение 90 дней по умолчанию, и нет платы за это 90 дней хранения. Эти типы данных также свободны от сборов за проглатку данных. 

### <a name="retention-by-data-type"></a>Сохранение по типу данных

Также можно указать различные параметры хранения для отдельных типов данных от 30 до 730 дней (за исключением рабочих областей в устаревшем уровне free pricing). Каждый тип данных является субресурсом рабочей области. Например, таблица SecurityEvent может быть рассмотрена в [azure Resource Manager:](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Обратите внимание, что тип данных (таблица) является конфиденциальным случаем.  Для получения текущих параметров хранения типа данных определенного типа данных (в данном примере SecurityEvent) используйте:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Чтобы получить текущие параметры хранения типа данных для всех типов данных в рабочей области, просто опустите определенный тип данных, например:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Чтобы установить сохранение определенного типа данных (в данном примере SecurityEvent) до 730 дней,

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Допустимые `retentionInDays` значения для от 30 до 730.

`Usage` Типы `AzureActivity` данных и данных не могут быть установлены с пользовательским сохранением. Они будут принимать на максимуме удержания рабочего пространства по умолчанию или 90 дней. 

Отличным инструментом для подключения непосредственно к менеджеру ресурсов Azure для установки удержания по типу данных является инструмент OSS [ARMclient.](https://github.com/projectkudu/ARMClient)  Узнайте больше о ARMclient из статей [Дэвида Эббо](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) и [Дэниела Боуби .](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)  Вот пример использования ARMClient, установив данные SecurityEvent на 730 дней удержания:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Установка удержания отдельных типов данных может быть использована для снижения затрат на хранение данных.  Для данных, собранных начиная с октября 2019 года (когда эта функция была выпущена), сокращение хранения для некоторых типов данных может снизить затраты на хранение с течением времени.  Для данных, собранных ранее, установление более низкого удержания для отдельного типа не повлияет на ваши затраты на удержание.  

## <a name="manage-your-maximum-daily-data-volume"></a>Управление максимальным ежедневным объемом данных

Вы можете настроить ежедневное ограничение и ограничить ежедневный прием данных для своей рабочей области, но будьте осторожны, так как ваша цель не должна превышать дневной лимит.  В противном случае в этот момент вы потеряете данные за оставшуюся часть дня, что может повлиять на доступность в рабочей области служб и решений Azure, функциональные возможности которых зависят от актуальных данных.  В результате вы не сможете наблюдать за условиями работоспособности ресурсов, поддерживающих ИТ-службы, и получать соответствующие уведомления.  Дневной лимит предназначен для использования в качестве способа управления неожиданным увеличением объема данных из управляемых ресурсов и пребывания в пределах вашего предела, или когда вы хотите ограничить незапланированные расходы для рабочего пространства.  

Вскоре после достижения дневного лимита сбор оплачиваемых типов данных прекращается до конца дня. (Задержка, присущая применению дневного лимита, может означать, что крышка не применяется как точно указанный дневной уровень крышки.) Предупреждающий баннер отображается в верхней части страницы для выбранного рабочего пространства Log Analytics, а событие операции отправляется в таблицу *операции* в категории **LogManagement.** Сбор данных возобновится по наступлении времени сброса, определенного в разделе *Daily limit will be set at* (Ежедневное ограничение будет установлено в). Рекомендуем определить правило генерации оповещений на основе этого события операции, настроенного для уведомления о достижении ежедневного ограничения по сбору данных. 

> [!WARNING]
> Ежедневная крышка не останавливает сбор данных из Центра безопасности Azure, за исключением рабочих областей, в которых до 19 июня 2017 года был установлен Центр безопасности Azure. 

### <a name="identify-what-daily-data-limit-to-define"></a>Определение ежедневного ограничения по сбору данных

Сведения о тенденциях приема данных и определении ежедневного ограничения для объема см. в статье [Анализ использования данных в службе Log Analytics](usage-estimated-costs.md). Следует рассматривать его с осторожностью, так как вы не сможете контролировать свои ресурсы после достижения лимита. 

### <a name="set-the-daily-cap"></a>Установить ежедневную крышку

Следующие шаги описывают, как настроить лимит для управления объемом данных, которые рабочее пространство Log Analytics будет глотать в день.  

1. В рабочей области на панели слева выберите пункт **Usage and estimated costs** (Использование и ожидаемые затраты).
2. В верхней части страницы **Usage and estimated costs** (Использование и ожидаемые затраты) для выбранной рабочей области щелкните **Управление объемом данных**. 
3. Дневной лимит **OFF** по умолчанию нажмите **ON,** чтобы включить его, а затем установить предел объема данных в ГБ / день.

    ![Настройка ограничения сбора данных в Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Предупреждение, когда Ежедневная крышка достигла

Когда достигнуто ограничение сбора данных, на портале Azure отображается соответствующее сообщение. Однако, возможно, вы управляете операционными проблемами, требующими немедленного внимания, иным образом.  Чтобы получать оповещение, вы можете создать новое правило генерации оповещений в Azure Monitor.  Чтобы узнать больше, узнайте, [как создавать, просматривать и управлять оповещениями.](alerts-metric.md)

Перед началом работы просмотрите рекомендуемые настройки для оповещения:

- Цель: выберите рабочую область Log Analytics.
- Критерии: 
   - Название сигнала: "Пользовательский поиск по журналам".
   - Поисковый запрос: Operation | где в Detail указано OverQuota.
   - Основа: число результатов.
   - Условие: "больше чем".
   - Пороговое значение: 0.
   - Период: 5 (в минутах).
   - Периодичность: 5 (в минутах).
- Название правила генерации оповещений: "Достигнуто ежедневное ограничение сбора данных".
- Серьезность: предупреждение (серьезность 1).

Как только будет определено оповещение и достигнут предел, активируется предупреждение и будет выполнен ответ, определенный в группе действий. Ваша команда может получать сообщение по электронной почте и текстовое сообщение, могут быть автоматизированы действия с помощью веб-перехватчиков, модулей Runbook службы автоматизации или же выполнена [интеграция с внешним решением ITSM](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Превышенный объем данных: причины и устранение

Превышенное использование вызывается одной (или двумя) причинами:
- Больше узлов, чем ожидалось, отправка данных в рабочее пространство Log Analytics
- Больше данных, чем ожидалось, направляется в рабочее пространство Log Analytics (возможно, из-за начала использования нового решения или изменения конфигурации существующего решения)

## <a name="understanding-nodes-sending-data"></a>Понимание узлов отправки данных

Чтобы понять количество узлов, сообщающих об ударе ныхсердце от агента каждый день в прошлом месяце, используйте

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Получить подсчет узлов, отправляющих данные за последние 24 часа, использует запрос: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Для получения списка узлов, отправляющих любые данные (и объем данных, отправленных каждым из них), можно использовать последующий запрос:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> Используйте эти запросы `union withsource = tt *` только в случае необходимости, так как сканирование по типам данных требует больших затрат на выполнение. Этот запрос заменяет старый способ запроса на компьютерную информацию на тип данных «Использование».  

## <a name="understanding-ingested-data-volume"></a>Понимание объема данных

На странице **Использование и ожидаемые затраты** есть диаграмма *Data ingestion per solution* (Прием данных по решениям), которая позволяет отобразить объем отправленных данных в целом и для каждого решения отдельно. Это позволяет выявить некоторые тенденции, например оценить изменения общего объема используемых данных (или по определенному решению). 

### <a name="data-volume-for-specific-events"></a>Объем данных для конкретных событий

Чтобы посмотреть на размер просаченных данных для определенного набора событий, `Event`можно запросить конкретную таблицу (в данном примере), а затем ограничить запрос событиями, представляющими интерес (в этом примере ID 5145 или 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Обратите внимание, `where IsBillable = true` что положение отфильтровывает типы данных из определенных решений, для которых нет платы за проглатку. 

### <a name="data-volume-by-solution"></a>объем данных для каждого решения;

Запрос, используемый для просмотра оплачиваемого объема данных по решению за последний месяц (за исключением последнего частичного дня),:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Положение, `TimeGenerated` с которым и состоит только для того, чтобы гарантировать, что опыт запроса на портале Azure будет оглядываться за 24 часа. При использовании типа `StartTime` данных использования и `EndTime` представляют временные ведра, для которых представлены результаты. 

### <a name="data-volume-by-type"></a>Объем данных по типам

Вы можете просверлить в дальнейшем, чтобы увидеть тенденции данных по типу данных:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Или чтобы увидеть таблицу по решению и типу за последний месяц,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Объем данных с помощью компьютера

Тип `Usage` данных не включает информацию на уровне completer. Чтобы увидеть **размер** просачиваемых `_BilledSize` данных на компьютер, используйте [свойство,](log-standard-properties.md#_billedsize)которое обеспечивает размер байтов:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [Свойство](log-standard-properties.md#_isbillable) определяет, понесут ли просачиваемые данные расходы.

Чтобы увидеть **количество** оплачиваемых событий, посягнунных на компьютер, используйте 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Объем данных по ресурсам Azure, группе ресурсов или подписке

Для данных из узлов, размещенных в Azure, вы можете получить **размер** прослушных данных __на компьютер,__ используйте [свойство](log-standard-properties.md#_resourceid)_ResourceId, которое обеспечивает полный путь к ресурсу:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Для данных из узлов, размещенных в Azure, вы можете получить **размер** проглоченных данных __по подписке Azure,__ разогнав свойство `_ResourceId` по следующим образом:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Изменение `subscriptionId` `resourceGroup` будет отображать оплачиваемый объем данных группой ресурсов Azure. 

> [!WARNING]
> Некоторые поля с типом данных Usage (Потребление) уже устарели и данные в них не заполняются, хотя они пока сохраняются в схеме. Например, сюда относятся поля **Computer** и ряд данных о приеме данных (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** и **AverageProcessingTimeMs**).

### <a name="querying-for-common-data-types"></a>Запрос для общих типов данных

Чтобы получить более подробную информацию об источнике данных по определенному типу данных, воспользуйтесь приведенными ниже примерами запросов.

+ Решение по **безопасности**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Решение для **управления журналами**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Тип данных **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Тип данных **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Тип данных **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Тип данных **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Советы по снижению объемов данных

Вот несколько рекомендаций, которые помогут снизить объем собираемых журналов.

| Источник превышенного объема данных | Как сократить объем данных |
| -------------------------- | ------------------------- |
| События безопасности            | Выберите [события со стандартным или минимальным уровнем безопасности](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier). <br> Измените политику аудита безопасности таким образом, чтобы собирать только необходимые события. В частности проверьте необходимость сбора следующих событий: <br> - [аудит платформы фильтрации](https://technet.microsoft.com/library/dd772749(WS.10).aspx); <br> - [аудит реестра](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10));<br> - [аудит файловой системы](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10));<br> - [аудит объектов ядра](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10));<br> - [аудит работы с дескрипторами](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10));<br> — аудит съемных носителей. |
| Счетчики производительности       | Измените [конфигурацию счетчика производительности](data-sources-performance-counters.md), чтобы <br> уменьшить частоту сбора или <br> сократить число счетчиков производительности. |
| Журналы событий                 | Измените [конфигурацию журнала событий](data-sources-windows-events.md), чтобы <br> сократить число собранных журналов событий или <br> выполнять сбор только необходимых уровней событий. Например, не выполняйте сбор событий уровня *сведений*. |
| Системный журнал                     | Измените [конфигурацию системного журнала](data-sources-syslog.md), чтобы <br> сократить число собранных объектов или <br> выполнять сбор только необходимых уровней событий. Например, не выполняйте сбор событий уровня *сведений* и *отладки*. |
| AzureDiagnostics           | Измените коллекцию журнала ресурсов, чтобы: <br> Уменьшить число ресурсов, отправляющих журналы в Log Analytics. <br> Выполнять сбор только необходимых журналов. |
| Данные решений с компьютеров, которым не требуется решение | Используйте [таргетинг решения](../insights/solution-targeting.md) для сбора данных только из необходимых групп компьютеров. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Получение узлов в качестве счета в уровне ценообразования Per Node

Чтобы получить список компьютеров, которые будут выставлены в качестве узлов, если рабочее пространство находится в устаревшем уровне ценообразования Per Node, ищите узлы, которые отправляют **выставленные на счет типы данных** (некоторые типы данных бесплатны). Для этого используйте `_IsBillable` [свойство](log-standard-properties.md#_isbillable) и используйте левое поле полностью квалифицированного доменного имени. Это возвращает количество компьютеров с выставленными на счет данных в час (что является детализацией, при которой узлы учитываются и выставляются счета):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Количество узлов безопасности и автоматизации

Если вы используете ценовую категорию "За узел (OMS)", плата взимается в зависимости от количества используемых узлов и решений. Количество узлов для предложения "Аналитика", для которых выставляются счета, отображается в таблице на странице **Использование и предполагаемые затраты**.  

Чтобы просмотреть количество отдельных узлов безопасности, выполните такой запрос:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Чтобы просмотреть количество отдельных узлов службы автоматизации, выполните такой запрос:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Оценка уровня цен на устаревший уровень цен Per Node

Решение о том, являются ли рабочие области с доступом к устаревшему уровню ценообразования **Per Node** более выгодным в этом уровне или в текущем уровне **оплаты** по мере использования или **резервированию емкости,** часто бывает трудным для клиентов.  Это включает в себя понимание компромисса между фиксированной стоимостью на контролируемый узло в уровне ценообразования Per Node и включенным в него распределением данных в размере 500 МБ/узла/день и затратами на простое оплату данных в уровне Pay-As-You-Go (Per GB). 

Для облегчения этой оценки можно использовать следующий запрос для выполнения рекомендаций по оптимальному уровню ценообразования на основе шаблонов использования рабочей области.  В этом запросе рассматриваются контролируемые узлы и данные, попадавшие в рабочее пространство за последние 7 дней, и каждый день оценивается, какой уровень ценообразования был бы оптимальным. Чтобы использовать запрос, необходимо указать, использует ли рабочее пространство `workspaceHasSecurityCenter` Центр `true` `false`безопасности Azure, установив или, а затем (по желанию) обновление цен Per Node и Per GB, которые получает ваш организитон. 

```kusto
// Set these paramaters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your price per node / month 
let PerGBPrice = 2.30; // Enter your price per GB 
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
union withsource = tt * 
| where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join (
    Usage 
    | where TimeGenerated > ago(8d)
    | where StartTime >= startofday(now(-7d)) and EndTime < startofday(now())
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*nodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 1.0*nodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerGBPrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
| project day, Recommendation // Comment this line to see details
| sort by day asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>Создание оповещения при высоком уровне сбора данных

В этом разделе описывается создание оповещения для следующих случаев:
- объем данных превышает заданный объем;
- объем данных превысит заданный объем.

Служба "Оповещения Azure" поддерживает [оповещения журналов](alerts-unified-log.md), использующие поисковые запросы. 

Следующий запрос содержит результат, когда за сутки собрано более 100 ГБ данных:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

Следующий запрос использует простую формулу для прогноза, что в течение 24 часов будет отправлено более 100 ГБ данных: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

Чтобы создать оповещения для различных объемов данных, измените значение "100" в запросах на число ГБ, для которых вы хотите создать оповещения.

Выполните действия, описанные в разделе о [создании оповещений журналов](alerts-metric.md), чтобы получать уведомления при превышении ожидаемого объема собираемых данных.

При создании оповещения для первого запроса, когда имеется объем данных, превышающий 100 ГБ в сутки, задайте параметрам следующие значения:  

- Для параметра **Определение условия оповещения** укажите вашу рабочую область Log Analytics в качестве целевого ресурса.
- Для **критериев оповещения** укажите следующее:
   - **Имя сигнала** выберите **пользовательский поиск журнала**
   - **поисковому запросу** — `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`;
   - **логика оповещений** должна быть **основана на** *числе результатов*, а значение **условия** должно быть *больше***порогового значения***0*;
   - укажите **период времени***1440* минут, а для **частоты оповещений** задайте каждые *60* минут, так как данные об использовании обновляются раз в час.
- В разделе **Определение сведений об оповещении** задайте такие значения:
   - **имени** — *объем данных, превышающий 100 ГБ в сутки*;
   - **серьезности** — *предупреждение*;

Укажите существующую или создайте новую [группу действий](action-groups.md), чтобы получать соответствующее уведомление, когда оповещение журнала соответствует заданным критериям.

При создании оповещения для второго запроса, когда спрогнозировано, что суточный объем данных превысит 100 ГБ, задайте параметрам следующие значения:

- Для параметра **Определение условия оповещения** укажите вашу рабочую область Log Analytics в качестве целевого ресурса.
- Для **критериев оповещения** укажите следующее:
   - **Имя сигнала** выберите **пользовательский поиск журнала**
   - **поисковому запросу** — `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`;
   - **логика оповещений** должна быть **основана на** *числе результатов*, а значение **условия** должно быть *больше***порогового значения***0*;
   - укажите **период времени***180* минут, а для **частоты оповещений** задайте каждые *60* минут, так как данные об использовании обновляются один раз в час.
- В разделе **Определение сведений об оповещении** задайте такие значения:
   - **имени** — *объем данных, превышающий 100 ГБ в сутки*;
   - **серьезности** — *предупреждение*;

Укажите существующую или создайте новую [группу действий](action-groups.md), чтобы получать соответствующее уведомление, когда оповещение журнала соответствует заданным критериям.

Когда вы получите оповещение, выполните действия, описанные в следующем разделе, чтобы определить причину использования превышенного объема данных.

## <a name="data-transfer-charges-using-log-analytics"></a>Плата за передачу данных с помощью log Analytics

Отправка данных в журнал Analytics может повлечь за себя плату за пропускную способность данных. Как описано на [странице ценообразования Azure Bandwidth,](https://azure.microsoft.com/pricing/details/bandwidth/)передача данных между службами Azure, расположенными в двух регионах, взимается как передача исходящих данных с обычной скоростью. Входная передача данных бесплатна. Тем не менее, эта плата очень мала (несколько %) по сравнению с затратами на проглатку данных Log Analytics. Следовательно, контроль затрат на log Analytics должен быть сосредоточен на объеме данных, и у нас есть рекомендации, которые помогут понять это [здесь.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Почему Log Analytics больше не собирает данные

Если вы используете устаревшую бесплатную ценовую категорию и отправили больше 500 МБ данных за день, сбор данных останавливается до конца дня. Достижение ежедневного ограничения является распространенной причиной, по которой Log Analytics прекращает сбор данных или по которой данные отсутствуют.  Log Analytics создает событие типа "Операция", когда сбор данных начинается и останавливается. Выполните следующий запрос в поле поиска, чтобы проверить, достигнут ли лимит и отсутствуют ли данные: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

При остановке сбора данных предупреждение «ОперацияСтатус» — это **предупреждение.** При запуске сбора данных operationStatus **успешно работает.** В следующей таблице описаны причины, по которым сбор данных останавливается, и приведены рекомендуемые действия, чтобы его возобновить:  

|Причина прекращения сбора| Решение| 
|-----------------------|---------|
|Достигнут ежедневный предел устаревшей бесплатной ценовой категории. |Дождитесь следующего дня для автоматического перезапуска сбора или перейдите на платную ценовую категорию.|
|Достигнуто ежедневное ограничение рабочей области.|Дождитесь автоматического перезапуска сбора или увеличьте предел ежедневного объема собираемых данных, как описано в разделе управления максимальным ежедневным объемом данных. Время сброса ежедневного ограничения отображается на странице **Управление объемом данных**. |
|Подписка Azure находится в состоянии "Приостановлено" по причине:<br> Период бесплатной пробной версии завершен<br> Истек срок действия Azure Pass<br> Достигнут лимит ежемесячной суммы расходов (например, на подписку MSDN или Visual Studio)|Измените подписку на платную<br> Удалите ограничение или подождите, пока оно сбросится|

Чтобы получать уведомления при остановке сбора данных, используйте шаги, описанные в *Оповещении о дневном лимите данных,* чтобы получать уведомления при остановке сбора данных. Используйте шаги, описанные в [создании группы действий,](action-groups.md) чтобы настроить электронную почту, веб-крюк или действие runbook для правила оповещения. 

## <a name="limits-summary"></a>Сводная таблица ограничений

Существуют некоторые дополнительные ограничения Log Analytics, некоторые из которых зависят от уровня ценообразования Log Analytics. Это описано [здесь](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Следующие шаги

- Смотрите [поиск в журналах Журналы мониторинга Azure,](../log-query/log-query-overview.md) чтобы узнать, как использовать язык поиска. Вы можете использовать поисковые запросы, чтобы выполнить дополнительный анализ данных об использовании.
- Выполните действия, описанные в разделе о [создании оповещений журналов](alerts-metric.md), чтобы получать уведомления при выполнении условий поиска.
- Используйте [таргетинг решения](../insights/solution-targeting.md) для сбора данных только из необходимых групп компьютеров.
- Сведения о настройке эффективной политики сбора событий см. в статье [Сбор данных в Центре безопасности Azure](../../security-center/security-center-enable-data-collection.md).
- Изменение [конфигурации счетчика производительности.](data-sources-performance-counters.md)
- Чтобы изменить настройки сбора событий, просмотрите [конфигурацию журнала событий.](data-sources-windows-events.md)
- Чтобы изменить настройки коллекции слогов, просмотрите [конфигурацию syslog.](data-sources-syslog.md)