---
title: Включить монитор Azure для виртуальных машин (Предварительная версия) с помощью шаблона Resource Manager или Azure PowerShell | Документация Майкрософт
description: В этой статье описывается, как включить Azure Monitor для виртуальных машин для одного или нескольких виртуальных машинах или масштабируемых наборов виртуальных машин с помощью шаблонов Azure Resource Manager или Azure PowerShell.
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
ms.openlocfilehash: a22bc88fb066d9b845f7fdf1592e2194a03915bc
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524135"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-template"></a>Включить монитор Azure для виртуальных машин (Предварительная версия) с помощью шаблона Resource Manager или Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

В этой статье объясняется, как включить Azure Monitor для виртуальных машин (Предварительная версия) для виртуальных машин Azure или масштабируемые наборы виртуальных машин с помощью шаблонов Azure Resource Manager или Azure PowerShell. В конце этого процесса, который будет успешно начала мониторинга всех виртуальных машин и узнайте, если любой возникают производительности или проблемы с доступностью. 

## <a name="set-up-a-log-analytics-workspace"></a>Настройка рабочей области Log Analytics 

Если у вас нет рабочей области Log Analytics, создайте его, просмотрев методы, предлагаемые в [предварительные требования](vminsights-enable-overview.md#log-analytics) раздела перед выполнением шагов, чтобы настроить его, чтобы завершить развертывание Azure Monitor для Виртуальные машины с помощью метода шаблона Azure Resource Manager.

### <a name="enable-performance-counters"></a>Включение счетчиков производительности

Если указанная в решении рабочая область Log Analytics еще не настроена для сбора данных от необходимых решению счетчиков производительности, их необходимо включить отдельно. Это можно сделать одним из двух способов:
* вручную, как описано в статье [Источники данных о производительности Windows и Linux в Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md);
* загрузив и запустив сценарий PowerShell, доступный в [коллекции Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).

### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Установите решения ServiceMap и InfrastructureInsights.
В этом методе используется шаблон JSON, который задает конфигурацию для включения компонентов решения в рабочей области Log Analytics.

Если вы не знакомы с развертыванием ресурсов с помощью шаблона, см. статьи:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

        Изменение конфигурации может занять несколько минут. После настройки появится сообщение, похожее на приведенное ниже, с таким результатом:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Выполните следующую команду с помощью Azure CLI:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-azure-resource-manager-template"></a>Включить с помощью шаблона Azure Resource Manager
Мы создали пример шаблонов Azure Resource Manager для подключения виртуальных машин и масштабируемых наборов виртуальных машин. Эти шаблоны включают в себя сценарии для включения мониторинга для существующего ресурса, а также для создания нового ресурса, мониторинг включен.

>[!NOTE]
>Шаблон должен быть развернут в той же группе ресурсов, что и ресурс нужно подключить.

Если вы не знакомы с концепцией развертывания ресурсов с помощью шаблона, ознакомьтесь со статьями:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="download-templates"></a>Загрузка шаблонов

Шаблоны Azure Resource Manager приведены в файл архива (ZIP-файл), который вы можете [загрузить](https://aka.ms/VmInsightsARMTemplates) из репозитория GitHub. Содержимое файла содержат папки, представляющий каждого сценария развертывания с помощью файла шаблона и параметров. Перед запуском, измените файл параметров и укажите необходимые значения. Не изменяйте файл шаблона, если не нужно настроить его в соответствии с требованиями конкретного. После изменения файла параметров можно развернуть его с помощью следующих методов, описанных далее в этой статье. 

Загружаемый файл содержит следующие шаблоны для различных сценариев:

- **ExistingVmOnboarding** шаблон позволяет Azure Monitor для виртуальных машин, если виртуальные машины уже существует.
- **NewVmOnboarding** шаблон создает виртуальную машину и включение монитора Azure для виртуальных машин, для него отслеживания программой.
- **ExistingVmssOnboarding** шаблон позволяет Azure Monitor для виртуальных машин, если виртуальной машины масштабируемого набора уже существует.
- **NewVmssOnboarding** шаблон создает наборы масштабирования виртуальных машин и включение монитора Azure для виртуальных машин, отслеживать их.
- **ConfigureWorksapce*** шаблон настраивает рабочую область Log Analytics для поддержки Azure Monitor для виртуальных машин, позволяя решения и сбор данных счетчиков производительности операционной системы Linux и Windows.

>[!NOTE]
>Если уже имелись масштабируемые наборы виртуальных машин и политике обновления задано значение **вручную**, Azure Monitor для виртуальных машин не будет включен для экземпляров по умолчанию после выполнения команды **ExistingVmssOnboarding** Шаблон Azure Resource Manager. Необходимо вручную обновить экземпляры.

### <a name="deploy-using-azure-powershell"></a>Развертывание с помощью Azure PowerShell

Следующий шаг включает мониторинг с помощью Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

```powershell
provisioningState       : Succeeded
```
### <a name="deploy-using-azure-cli"></a>Развертывание с помощью Azure CLI

Следующий шаг включает мониторинг с помощью Azure CLI.   

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Результат должен выглядеть так:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Включение с помощью PowerShell

Чтобы включить Azure Monitor для виртуальных машин на нескольких виртуальных машинах или в нескольких масштабируемых наборах виртуальных машин, можно использовать сценарий PowerShell [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), доступный в коллекции Azure PowerShell. В этом сценарии поочередно перебираются все виртуальные машины и масштабируемые наборы виртуальных машин в подписке в указанной с помощью параметра *ResourceGroup* группе ресурсов на одной виртуальной машине или в масштабируемом наборе, для которых указано *Имя*. Для каждой виртуальной машины или масштабируемого набора виртуальных машин в сценарии проверяется, установлено ли расширение виртуальной машины. Если расширение виртуальной машины не установлено, в сценарии выполняется повторная попытка его установки. Если расширение виртуальной машины установлено, в сценарии устанавливается расширение виртуальной машины для агента Log Analytics и Dependency Agent.

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
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


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

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

В следующем примере представлены команды PowerShell, которые позволяют включить Azure Monitor для виртуальных машин, и описан ожидаемый результат:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

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

Already Onboarded: (0)

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

Теперь, когда мониторинг включен для виртуальных машин, эти сведения можно получить для анализа с помощью Azure Monitor для виртуальных машин. Чтобы узнать, как использовать функцию работоспособности, см. статью о [просмотре сведений о работоспособности Azure Monitor для виртуальных машин](vminsights-health.md). Для просмотра обнаруженных зависимостей приложений см. статью о [просмотре схемы Azure Monitor для виртуальных машин](vminsights-maps.md). Чтобы выявить узкие места производительности и общий уровень загрузки для виртуальных машин, изучите статью о [просмотре данных производительности виртуальной машины Azure](vminsights-performance.md). Просмотреть обнаруженные зависимости приложений поможет статья о [функции карты Azure Monitor для виртуальных машин](vminsights-maps.md).