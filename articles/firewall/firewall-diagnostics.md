---
title: Мониторинг журналов и метрик Брандмауэра Azure
description: Из этой статьи вы узнаете, как включить журналы и метрики брандмауэра Azure и управлять ими.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 07/10/2020
ms.author: victorh
ms.openlocfilehash: 82202705c5dbd4539eec4775d0844a749fd405f9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537009"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Мониторинг журналов и метрик Брандмауэра Azure

Работу брандмауэра Azure можно отслеживать с помощью журналов брандмауэра. Также журналы действий можно использовать для аудита операций на ресурсах брандмауэра Azure. С помощью метрик можно просматривать счетчики производительности на портале.

Доступ к некоторым из этих журналов можно получить через портал. Журналы можно передавать в [Журналы Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), службу хранилища, Центры событий, а затем анализировать в Журналах Azure Monitor или при помощи различных инструментов, таких как Excel и Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать, ознакомьтесь с [журналами и метриками брандмауэра Azure](logs-and-metrics.md) , чтобы получить общие сведения о журналах диагностики и метриках, доступных для брандмауэра Azure.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Включение журнала ведения диагностики на портале Azure

Для включения ведения журнала диагностики потребуется несколько минут, чтобы данные появились в журналах после завершения этой процедуры. Если ничего не происходит, проверьте еще раз через несколько минут.

1. В портал Azure откройте группу ресурсов брандмауэра и выберите брандмауэр.
2. В разделе **Мониторинг** выберите **Параметры диагностики**.

   Для брандмауэра Azure доступны два журнала обслуживания.

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Выберите **Добавить параметр диагностики**. На странице **Параметры диагностики** представлены параметры журналов диагностики.
5. В этом примере журналы хранятся в Журналах Azure Monitor, поэтому введите имя **Firewall log analytics**.
6. В разделе **Журнал**выберите **азурефиреваллаппликатионруле** и **азурефиреваллнетворкруле** , чтобы получить журналы для правил приложений и сетей.
7. Выберите **отправить log Analytics** , чтобы настроить рабочую область.
8. Выберите свою подписку.
9. Щелкните **Сохранить**.

## <a name="enable-logging-with-powershell"></a>Включение ведения журнала с помощью PowerShell

Ведение журнала действий автоматически включается для каждого ресурса Resource Manager. Чтобы начать сбор соответствующих данных, журналы ведения диагностики должны быть включены.

Чтобы включить ведение журналов диагностики, выполните следующие действия.

1. Запишите или запомните ИД ресурса учетной записи хранения, где хранятся данные журнала, Это значение имеет вид: */Subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /провидерс/Микрософт.стораже/сторажеаккаунтс/ \<storage account name\> *.

   Можно использовать любую учетную запись хранения в подписке. Получить эти сведения можно на портале Azure. Нужные сведения можно найти на странице **Свойство** ресурса.

2. Запишите или запомните идентификатор ресурса брандмауэра, для которого нужно включить ведение журнала, Это значение имеет вид: */Subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /провидерс/Микрософт.Нетворк/азурефиреваллс/ \<Firewall name\> *.

   Получить эти сведения можно на портале.

3. Включите ведение журнала диагностики с помощью следующего командлета PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>Журналам диагностики отдельная учетная запись хранения не требуется. За использование хранилища для журналов доступа и производительности взимается плата.

## <a name="view-and-analyze-the-activity-log"></a>Просмотр и анализ журнала действий

Данные журнала действий можно просматривать и анализировать с помощью любого из следующих методов:

* **Средства Azure.** Информацию из журналов действий можно получать с помощью Azure PowerShell, Azure CLI, REST API Azure или портала Azure. Пошаговые инструкции для каждого метода подробно описаны в статье [Activity operations with Resource Manager](../azure-resource-manager/management/view-activity-logs.md) (Выполнение операций в журналах действий с помощью Resource Manager).
* **Power BI**. Если у вас еще нет учетной записи [Power BI](https://powerbi.microsoft.com/pricing) , вы можете попробовать ее бесплатно. Используя [пакет содержимого журналов действий Azure для Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), можно анализировать данные с помощью предварительно настроенных панелей мониторинга, которые можно использовать "как есть" или дополнительно настроить.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Просмотр и анализ журналов правил сети и приложений

Служба [Журналы Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md) собирает файлы счетчика и журнала событий. Эта служба предоставляет средства визуализации и эффективные возможности поиска для анализа журналов.

Примеры запросов к службе анализа журналов в Azure Firewall [см. в](log-analytics-samples.md)этой статье.

Вы также можете подключиться к учетной записи хранения и извлечь записи журнала JSON для журналов доступа и производительности. После скачивания JSON-файлов их можно преобразовать в формат CSV и просматривать в Excel, Power BI или другом средстве визуализации данных.

> [!TIP]
> Если вы знакомы с Visual Studio и основными понятиями изменения значений констант и переменных в C#, можно использовать [инструменты преобразования журналов](https://github.com/Azure-Samples/networking-dotnet-log-converter), доступные на сайте GitHub.

## <a name="view-metrics"></a>Просмотр метрик
Перейдите к брандмауэру Azure, в разделе **мониторинг** выберите **метрики**. Чтобы просмотреть доступные значения, выберите раскрывающийся список **Метрика**.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда брандмауэр настроен для сбора журналов, можно открыть Журналы Azure Monitor, чтобы просмотреть данные.

[Решения для мониторинга сетей в Журналах Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)
