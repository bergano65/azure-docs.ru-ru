---
title: Мониторинг операций, событий и счетчиков для общедоступной службы Load Balancer ценовой категории "Базовый"
titleSuffix: Azure Load Balancer
description: Узнайте, как включить ведение журналов событий оповещений и проверки работоспособности для общедоступного Load Balancer ценовой категории "Базовый"
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 23a3a2629c6f2f89c4b8f6d5af57bcf3b6bb67dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214913"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Azure Monitor logs for public Basic Load Balancer

>[!IMPORTANT] 
>Azure Load Balancer поддерживает два типа: категории "Базовый" и "Стандартный". В этой статье рассматривается Azure Load Balancer категории "Базовый". Дополнительные сведения о Load Balancer ценовой категории "Стандартный" см. в разделе [Обзор Azure Load Balancer уровня "Стандартный"](load-balancer-standard-overview.md), в котором предоставлены данные телеметрии с помощью многомерных метрик в Azure Monitor.

В Azure можно использовать различные виды журналов для управления Load Balancer ценовой категории "Базовый" и устранения возникающих в них неполадок. Доступ к некоторым из этих журналов можно получить через портал. Logs can be streamed to an event hub or a Log Analytics workspace. All logs can be extracted from Azure blob storage, and viewed in different tools, such as Excel and Power BI.  В списке ниже приведены дополнительные сведения о различных типах журналов.

* **Activity logs:** You can use [View activity logs to monitor actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) to view all activity being submitted to your Azure subscription(s), and their status. Activity logs are enabled by default, and can be viewed in the Azure portal.
* **Журналы событий оповещений.** Эти журналы можно использовать для просмотра оповещений, создаваемых для подсистемы балансировки нагрузки. Данные о состоянии балансировщика нагрузки собираются каждые пять минут. В этом журнале делается запись, только если возникает событие оповещения балансировщика нагрузки.
* **Журналы проверки работоспособности**. Эти журналы можно использовать для просмотра проблем, обнаруженных при проверке работоспособности, включая сведения о числе экземпляров во внутреннем пуле, которые не получают запросы от подсистемы балансировки нагрузки из-за ошибок проверки работоспособности. Эти журналы записываются при изменении состояния в ходе проверки работоспособности.

> [!IMPORTANT]
> Azure Monitor logs currently works only for public Basic load balancers. Журналы доступны только для ресурсов, развернутых в модели развертывания диспетчера ресурсов. Журналы нельзя использовать для ресурсов в классической модели развертывания. Дополнительные сведения о моделях развертывания см. в статье, посвященной [развертыванию с помощью Resource Manager и классическому развертыванию](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Включение ведения журналов

Ведение журнала действий автоматически включается для каждого ресурса Resource Manager. Enable event and health probe logging to start collecting the data available through those logs. Вот как можно включить ведение журнала:

Войдите на [портале Azure](https://portal.azure.com). Если у вас еще нет балансировщика нагрузки, [создайте балансировщик нагрузки](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) перед продолжением.

1. In the portal, click **Resource groups**.
2. Select **\<resource-group-name>** where your load balancer is.
3. Select your load balancer.
4. Select **Monitoring** > **Diagnostic settings**.
5. In the **Diagnostics settings** pane, under **Diagnostics settings**, select **+ Add diagnostic setting**.
6. In the **Diagnostics settings** creation pane, enter **myLBDiagnostics** in the **Name** field.
7. You have three options for the **Diagnostics settings**.  You can choose one, two or all three and configure each for your requirements:
   * **Archive to a storage account**
   * **Stream to an event hub**
   * **Send to Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>"Архивировать в учетной записи хранения";
    You'll need a storage account already created for this process.  To create a storage account, see [Create a storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Select the checkbox next to **Archive to a storage account**.
    2. Select **Configure** to open the **Select a storage account** pane.
    3. Select the **Subscription** where your storage account was created in the pull-down box.
    4. Select the name of your storage account under **Storage account** in the pull-down box. 
    5. Select OK.

    ### <a name="stream-to-an-event-hub"></a>"Передать в концентратор событий";
    You'll need an event hub already created for this process.  To create an event hub, see [Quickstart: Create an event hub using Azure portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Select the checkbox next to **Stream to an event hub**
    2. Select **Configure** to open the **Select event hub** pane.
    3. Select the **Subscription** where your event hub was created in the pull-down box.
    4. **Select event hub namespace** in the pull-down box.
    5. **Select event hub policy name** in the pull-down box.
    6. Select OK.

    ### <a name="send-to-log-analytics"></a>Отправка в Log Analytics
    You'll need to already have a log analytics workspace created and configured for this process.  To create a Log Analytics workspace, see [Create a Log Analytics workspace in the Azure portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Select the checkbox next to **Send to Log Analytics**.
    2. Select the **Subscription** where your Log Analytics workspace is in the pull-down box.
    3. Select the **Log Analytics Workspace** in the pull-down box.


8. Beneath the **LOG** section in the **Diagnostics settings** pane, select the check box next to both:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Beneath the **METRIC** section in the **Diagnostics settings** pane, select the check box next to:
   * **AllMetrics**

11. Verify everything looks correct and click **Save** at the top of the create **Diagnostic settings** pane.

## <a name="activity-log"></a>Журнал действий

The activity log is generated by default. Журналы хранятся в течение 90 дней в хранилище журналов событий Azure. Learn more about these logs by reading the [View activity logs to monitor actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) article.

## <a name="archive-to-storage-account-logs"></a>Archive to storage account logs

### <a name="alert-event-log"></a>Журнал событий оповещений

Этот журнал создается только в том случае, если он включен для конкретной подсистемы балансировки нагрузки. События регистрируются в формате JSON и хранятся в учетной записи хранения, указанной при включении ведения журнала. The following example is of an event.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

The JSON output shows the *eventname* property, which will describe the reason for the load balancer created an alert. In this case, the alert generated was because of TCP port exhaustion caused by source IP NAT limits (SNAT).

### <a name="health-probe-log"></a>Журнал проверки работоспособности

Этот журнал создается только в том случае, если он включен для конкретного балансировщика нагрузки, как описано выше. Данные хранятся в учетной записи хранения, указанной при включении ведения журнала. Создается контейнер с именем insights-logs-loadbalancerprobehealthstatus, и в журнал записываются следующие данные.

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

В выходных данных JSON в поле свойств отображаются основные сведения о состоянии проверки работоспособности. The *dipDownCount* property shows the total number of instances on the back-end, which are not receiving network traffic because of failed probe responses.

### <a name="view-and-analyze-the-audit-log"></a>Просмотр и анализ журнала аудита

Данные журнала аудита можно просматривать и анализировать с помощью любого из следующих методов.

* **Azure tools:** Retrieve information from the audit logs through Azure PowerShell, the Azure Command Line Interface (CLI), the Azure REST API, or the Azure portal. Пошаговые инструкции для каждого метода подробно описаны в статье [Операции аудита с помощью диспетчера ресурсов](../azure-resource-manager/resource-group-audit.md) .
* **Power BI.** Если у вас еще нет учетной записи [Power BI](https:// .microsoft.com/pricing) , ее можно опробовать бесплатно. Используя [пакет содержимого "Журналы аудита Azure" для Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs), можно анализировать данные с помощью предварительно настроенных панелей мониторинга или дополнительно настроить представление данных в зависимости от своих потребностей.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Просмотр и анализ журналов событий и проверки работоспособности

Connect to your storage account and retrieve the JSON log entries for event and health probe logs. Once you download the JSON files, you can convert them to CSV and view in Excel, Power BI, or any other data visualization tool.

> [!TIP]
> Если вы знакомы с Visual Studio и основными понятиями изменения значений констант и переменных в C#, можно использовать [инструменты преобразования журналов](https://github.com/Azure-Samples/networking-dotnet-log-converter), доступные на сайте GitHub.

## <a name="stream-to-an-event-hub"></a>"Передать в концентратор событий";
When diagnostic information is streamed to an event hub, it can be used for centralized log analysis in a third-party SIEM tool with Azure Monitor Integration. For more information, see [Stream Azure monitoring data to an event hub](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Отправка в Log Analytics
Resources in Azure can have their diagnostic information sent directly to a Log Analytics workspace where complex queries can be run against the information for troubleshooting and analysis.  For more information, see [Collect Azure resource logs in Log Analytics workspace in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>Дальнейшие действия

[Основные сведения о пробах подсистемы балансировки нагрузки](load-balancer-custom-probe-overview.md)
