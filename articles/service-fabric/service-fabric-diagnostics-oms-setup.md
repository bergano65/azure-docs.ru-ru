---
title: Настройка Azure Service Fabric. мониторинг с помощью журналов Azure Monitor | Документация Майкрософт
description: Узнайте, как настроить журналы Azure Monitor для визуализации и анализе событий для мониторинга кластеров Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: ca5dd4a7c12a68a549f081ad62db1736c9c68837
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483171"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Настроить журналы Azure Monitor для кластера

Журналы Azure Monitor — это мы рекомендуем использовать для отслеживания событий на уровне кластера. Рабочую область Log Analytics можно настроить в Azure Resource Manager, PowerShell или с помощью Azure Marketplace. Если необходимо сохранить обновленный шаблон Resource Manager развертывания для использования в будущем, используйте тот же шаблон для настройки среды журналы Azure Monitor. Развертывание через Marketplace проще, если вы уже развернули кластер с включенной диагностикой. Если у вас нет доступа на уровне подписки в учетной записи, в которую производится развертывание, используйте PowerShell или выполните развертывание с помощью шаблона Resource Manager.

> [!NOTE]
> Чтобы настроить журналы Azure Monitor для мониторинга кластера, необходимо включить систему диагностики для просмотра событий уровня кластера или на уровне платформы. Дополнительные сведения можно получить из статей [Агрегирование и сбор событий с помощью системы диагностики Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md) и [Агрегирование и сбор событий с помощью системы диагностики Azure для Linux](service-fabric-diagnostics-event-aggregation-lad.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Развертывание рабочей области Log Analytics с помощью Azure Marketplace

Если нужно добавить рабочую область Log Analytics после развертывания кластера, перейдите в Azure Marketplace на портале и найдите решение **аналитики Service Fabric**. Это пользовательское решение для развертывания Service Fabric, содержащее данные, относящиеся к Service Fabric. При этом будет создано и решение (панель мониторинга для просмотра аналитики), и рабочая область (агрегирование базовых данных кластера).

1. В области навигации слева щелкните **Создать**. 

2. Найдите **Аналитика Service Fabric**. Выберите появившийся ресурс.

3. Нажмите кнопку **Создать**.

    ![Аналитика Service Fabric в Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. В окне создания аналитики Service Fabric щелкните **Выберите рабочую область** для поля **Рабочая область OMS**, а затем выберите **Создание рабочей области**. Укажите необходимые записи. Единственным требованием является совпадение подписки для кластера Service Fabric и рабочей области. Когда завершится проверка записей, начнется развертывание рабочей области. Это займет всего несколько минут.

5. Когда процесс завершится, снова выберите **Создать** в нижней части окна создания аналитики Service Fabric. Убедитесь, что новая рабочая область отображается в разделе **Рабочая область OMS**. В таком случае решение добавится в созданную рабочую область.

Если вы используете Windows, выполните следующие действия для подключения Azure Monitor журналы в учетную запись хранения, где хранятся события кластера. 

>[!NOTE]
>Для кластеров Linux эта функция подключения еще недоступна. 

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Подключение рабочей области Log Analytics к кластеру 

1. Рабочая область должна быть подключена к данным диагностики, поступающим из кластера. Перейдите в группу ресурсов, в которой вы создали решение "Аналитика Service Fabric". Выберите **ServiceFabric\<\>** и перейдите на страницу обзора. На этой странице можно изменить параметры решения, рабочей области и получить доступ к рабочей области Log Analytics.

2. В меню навигации слева в разделе **Источники данных рабочей области** выберите **Журналы учетных записей хранения**.

3. На странице **Журналы учетной записи хранения** выберите **Добавить** в верхней части, чтобы добавить в рабочую область журналы кластера.

4. Выберите **Учетная запись хранения**, чтобы добавить учетную запись хранения, созданную в кластере. Если вы использовали имя по умолчанию, она называется **sfdg\<имя_группы_ресурсов\>**. Вы можете проверить, какое значение указано для параметра **applicationDiagnosticsStorageAccountName** в шаблоне Azure Resource Manager, на основе которого вы развертывали кластер. Если имя не отображается, прокрутите список вниз и выберите **Загрузить еще**. Выберите имя учетной записи хранения.

5. Укажите тип данных. Задайте для параметра типа данных значение **События Service Fabric**.

6. При этом для параметра "Источник" будет автоматически установлено значение **WADServiceFabric\*EventTable**.

7. Нажмите кнопку **ОК**, чтобы подключить рабочую область к журналам кластера.

    ![Добавить журналы учетной записи хранения для журналов Azure Monitor](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Теперь учетная запись будет отображаться в списке журналов учетной записи хранения в разделе источников данных для рабочей области.

Вы успешно добавили решение "Аналитика Service Fabric" в рабочую область Log Analytics, подключенную должным образом к платформе кластера и таблице журналов приложения. Аналогичным образом вы можете добавить в рабочую область дополнительные источники.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Развертывание Azure Monitor журналы с помощью Azure Resource Manager

При развертывании кластера с помощью шаблона Resource Manager этот шаблон создает рабочую область Log Analytics, добавляет в нее решение Service Fabric и настраивает в ней чтение данных из соответствующих таблиц службы хранилища.

Вы можете использовать [этот пример шаблона](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure), а также изменить его в соответствии со своими потребностями. Этот шаблон делает следующее:

* создает кластер Service Fabric с 5 узлами;
* создает рабочую область Log Analytics и решение Service Fabric;
* настраивает агент Log Analytics для сбора и отправки данных с 2 счетчиков производительности в рабочую область;
* настраивает WAD для сбора данных Service Fabric и отправляет их в таблицы хранилища Azure (WADServiceFabric*EventTable);
* настраивает рабочую область Log Analytics для считывания событий из этих таблиц.


Шаблон можно развернуть как обновление Resource Manager для кластера с помощью API `New-AzureRmResourceGroupDeployment` в модуле AzureRM PowerShell. Пример команды будет выглядеть так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager определит, что эта команда — это обновление имеющегося ресурса. Он обработает только изменения между шаблоном, управляющим имеющимся развертыванием, и новым предоставленным шаблоном.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Развертывание Azure Monitor журналов с помощью Azure PowerShell

Ресурс log analytics с помощью PowerShell можно также развернуть с помощью `New-AzureRmOperationalInsightsWorkspace` команды. Чтобы использовать этот метод, обязательно установите [Azure Powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Чтобы создать рабочую область Log Analytics и добавить в нее решение Service Fabric, используйте следующий сценарий: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Когда все будет готово, выполните действия, описанные в предыдущем разделе для подключения к соответствующей учетной записи хранения журналов Azure Monitor.

Вы также можете добавить другие решения или внести другие изменения в рабочую область Log Analytics с помощью PowerShell. Дополнительные сведения см. в разделе [управление Azure Monitor журналов с помощью PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Дальнейшие действия
* [Разверните агент Log Analytics](service-fabric-diagnostics-oms-agent.md) на узлах для сбора данных счетчиков производительности и статистики Docker, а также журналов для контейнеров.
* Ознакомьтесь с функциями [поиска и запроса журналов](../log-analytics/log-analytics-log-searches.md) функции, предоставляемые как часть журналов Azure Monitor
* [Использование конструктора представлений для создания пользовательских представлений в журналах Azure Monitor](../azure-monitor/platform/view-designer.md)
