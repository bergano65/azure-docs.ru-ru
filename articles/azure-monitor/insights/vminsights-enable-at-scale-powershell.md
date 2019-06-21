---
title: Включение Azure Monitor для виртуальных машин (Предварительная версия) с помощью Azure PowerShell или шаблонов Resource Manager | Документация Майкрософт
description: В этой статье описывается, как включить Azure Monitor для виртуальных машин для одного или более виртуальных машинах или масштабирования виртуальных машин задает с помощью Azure PowerShell или шаблонов Azure Resource Manager.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ff284ea0adf6021ace84cd6a41f0a0e4e987a9c8
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144247"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Включение Azure Monitor для виртуальных машин (Предварительная версия) с помощью Azure PowerShell или шаблонов Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

В этой статье объясняется, как включить Azure Monitor для виртуальных машин (Предварительная версия) для виртуальных машин Azure или масштабируемые наборы виртуальных машин с помощью шаблонов Azure Resource Manager или Azure PowerShell. В конце этого процесса, будет успешно начала мониторинга всех виртуальных машин и узнайте, если любой возникают производительности или проблемы с доступностью.

## <a name="set-up-a-log-analytics-workspace"></a>Настройка рабочей области Log Analytics 

Если у вас нет рабочей области Log Analytics, необходимо создать его. Просмотрите методы, предлагаемые в [предварительные требования](vminsights-enable-overview.md#log-analytics) разделе перед продолжением действия, чтобы настроить его. Затем вы можете завершить развертывание Azure Monitor для виртуальных машин с помощью метода шаблона Azure Resource Manager.

### <a name="enable-performance-counters"></a>Включение счетчиков производительности

Если указанная в решении рабочая область Log Analytics еще не настроена для сбора данных от необходимых решению счетчиков производительности, их необходимо включить отдельно. Это можно сделать одним из двух способов:
* вручную, как описано в статье [Источники данных о производительности Windows и Linux в Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md);
* Загрузив и запустив скрипт PowerShell, который доступен из [коллекции PowerShell Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Установите решения ServiceMap и InfrastructureInsights.
В этом методе используется шаблон JSON, который задает конфигурацию для включения компонентов решения в рабочей области Log Analytics.

Если вы не знаете, как для развертывания ресурсов с помощью шаблона, см. в разделе:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Чтобы использовать Azure CLI, необходимо сначала установить и использовать CLI локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Чтобы установить или обновить Azure CLI, см. в разделе [установите Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Скопируйте и вставьте в него следующий синтаксис JSON:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Сохраните этот файл в локальную папку, присвоив ему имя *installsolutionsforvminsights.json*.

1. Соберите значения параметров *WorkspaceName*, *ResourceGroupName* и *WorkspaceLocation*. Значение для *WorkspaceName* — это имя рабочей области Log Analytics. Значение *WorkspaceLocation* — регион, в котором определена рабочая область.

1. Теперь вы можете развернуть этот шаблон.
 
    * Выполните следующие команды PowerShell в папке с шаблоном:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Изменение конфигурации может занять несколько минут. После завершения, отображается сообщение, включает результат следующего вида:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Выполните следующую команду с помощью Azure CLI:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Изменение конфигурации может занять несколько минут. После завершения, отображается сообщение, включает результат следующего вида:

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Включение с помощью шаблонов Azure Resource Manager
Мы создали пример шаблонов Azure Resource Manager для подключения виртуальных машин и масштабируемых наборов виртуальных машин. Эти шаблоны включают в себя сценарии, которые можно использовать, чтобы включить мониторинг для существующего ресурса и создать новый ресурс с наблюдения за поддержкой.

>[!NOTE]
>Шаблон должен быть развернут в той же группе ресурсов, что и ресурс перевести на плате.

Если вы не знаете, как для развертывания ресурсов с помощью шаблона, см. в разделе:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Чтобы использовать Azure CLI, необходимо сначала установить и использовать CLI локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Чтобы установить или обновить Azure CLI, см. в разделе [установите Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Загрузка шаблонов

Шаблоны Azure Resource Manager приведены в файл архива (ZIP-файл), который вы можете [загрузить](https://aka.ms/VmInsightsARMTemplates) из репозитория GitHub. Содержимое файла включает папки, представляющие каждого сценария развертывания с помощью файла шаблона и параметров. Перед их запуском, измените файл параметров и укажите необходимые значения. Не изменять файл шаблона, если не нужно настроить его в соответствии с требованиями конкретного. После изменения файла параметров, его можно развернуть с помощью следующих методов, описанных далее в этой статье. 

Загружаемый файл содержит следующие шаблоны для различных сценариев:

- **ExistingVmOnboarding** шаблон позволяет Azure Monitor для виртуальных машин, если виртуальная машина уже существует.
- **NewVmOnboarding** шаблон создает виртуальную машину и включение монитора Azure для виртуальных машин, для него отслеживания программой.
- **ExistingVmssOnboarding** шаблон позволяет Azure Monitor для виртуальных машин, если виртуальной машины масштабируемого набора уже существует.
- **NewVmssOnboarding** шаблон создает масштабируемые наборы виртуальных машин и включение монитора Azure для виртуальных машин, отслеживать их.
- **ConfigureWorksapce** шаблон настраивает рабочую область Log Analytics для поддержки Azure Monitor для виртуальных машин, позволяя решения и сбор данных счетчиков производительности операционной системы Linux и Windows.

>[!NOTE]
>Если уже имелись масштабируемые наборы виртуальных машин и политике обновления задано значение **вручную**, Azure Monitor для виртуальных машин не будут включены для экземпляров по умолчанию после выполнения команды **ExistingVmssOnboarding** Шаблон Azure Resource Manager. Необходимо вручную обновить экземпляры.

### <a name="deploy-by-using-azure-powershell"></a>Развертывание с помощью Azure PowerShell

Следующий шаг включает мониторинг с помощью Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Изменение конфигурации может занять несколько минут. После завершения, отображается сообщение, включает результат следующего вида:

```powershell
provisioningState       : Succeeded
```
### <a name="deploy-by-using-the-azure-cli"></a>Развертывание с помощью Azure CLI

Следующий шаг включает мониторинг с помощью Azure CLI.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Результат выглядит следующим образом:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Включение с помощью PowerShell

Чтобы включить Azure Monitor для виртуальных машин для нескольких виртуальных машин и масштабируемых наборов виртуальных машин, используйте сценарий PowerShell [установки VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Он доступен из коллекции Azure PowerShell. Этот сценарий используется для итерации по:

- Каждой виртуальной машины и масштабируемого набора в вашей подписке виртуальных машин.
- Группе областью действия ресурсов, который задается параметром *ResourceGroup*. 
- Одной виртуальной Машины или виртуальной машины масштабируемый набор, который задается параметром *имя*.

Для каждой виртуальной машины или масштабируемого набора виртуальных машин в сценарии проверяется, установлено ли расширение виртуальной машины. Если расширение виртуальной Машины не установлена, сценарий пытается повторно установить его. Если расширение виртуальной машины установлено, в сценарии устанавливается расширение виртуальной машины для агента Log Analytics и Dependency Agent.

Для этого сценария требуется модуль Azure PowerShell Az версии 1.0.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). При использовании PowerShell на локальном компьютере также нужно запустить `Connect-AzAccount`, чтобы создать подключение к Azure.

Чтобы получить список сведений об аргументах сценария и пример использования, выполните команду `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

В следующем примере представлены команды PowerShell, которые позволяют включить Azure Monitor для виртуальных машин, и описан ожидаемый результат:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда мониторинг включен для виртуальных машин, эти сведения можно получить для анализа с помощью Azure Monitor для виртуальных машин.
 
- Чтобы узнать, как использовать функцию работоспособности, см. в разделе [представление Azure Monitor для виртуальных машин работоспособности](vminsights-health.md). 
- Для просмотра обнаруженных зависимостей приложений см. статью о [просмотре схемы Azure Monitor для виртуальных машин](vminsights-maps.md). 
- Чтобы определить узкие места и общее использование с производительностью виртуальной Машины, см. в разделе [производительность виртуальных Машин Azure представление](vminsights-performance.md). 
- Для просмотра обнаруженных зависимостей приложений см. статью о [просмотре схемы Azure Monitor для виртуальных машин](vminsights-maps.md).