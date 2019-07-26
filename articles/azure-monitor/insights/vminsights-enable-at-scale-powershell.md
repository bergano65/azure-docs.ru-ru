---
title: Включение Azure Monitor для виртуальных машин (Предварительная версия) с помощью шаблонов Azure PowerShell или диспетчер ресурсов | Документация Майкрософт
description: В этой статье описывается, как включить Azure Monitor для виртуальных машин для одной или нескольких виртуальных машин Azure или масштабируемых наборов виртуальных машин с помощью шаблонов Azure PowerShell или Azure Resource Manager.
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
ms.date: 07/09/2019
ms.author: magoedte
ms.openlocfilehash: 1025041ae69f2048a6c5396aaebb50b5fa884f86
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444170"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Включение Azure Monitor для виртуальных машин (Предварительная версия) с помощью шаблонов Azure PowerShell или диспетчер ресурсов

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

В этой статье объясняется, как включить Azure Monitor для виртуальных машин (Предварительная версия) для виртуальных машин Azure или масштабируемых наборов виртуальных машин с помощью шаблонов Azure PowerShell или Azure Resource Manager. По завершении этого процесса вы сможете успешно приступили к наблюдению за всеми виртуальными машинами и узнать, испытывает ли они проблемы с производительностью или доступности.

## <a name="set-up-a-log-analytics-workspace"></a>Настройка рабочей области Log Analytics 

Если у вас нет рабочей области Log Analytics, ее необходимо создать. Ознакомьтесь с методами, предлагаемыми в разделе [Предварительные требования](vminsights-enable-overview.md#log-analytics) , прежде чем продолжить процедуру настройки. Затем можно завершить развертывание Azure Monitor для виртуальных машин с помощью метода Azure Resource Manager шаблона.

### <a name="enable-performance-counters"></a>Включение счетчиков производительности

Если указанная в решении рабочая область Log Analytics еще не настроена для сбора данных от необходимых решению счетчиков производительности, их необходимо включить отдельно. Это можно сделать одним из двух способов:
* вручную, как описано в статье [Источники данных о производительности Windows и Linux в Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md);
* Путем скачивания и запуска скрипта PowerShell, доступного из [коллекции Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Установите решения ServiceMap и InfrastructureInsights.
В этом методе используется шаблон JSON, который задает конфигурацию для включения компонентов решения в рабочей области Log Analytics.

Если вы не умеете развертывать ресурсы с помощью шаблона, см. следующие материалы:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Чтобы использовать Azure CLI, сначала необходимо установить и использовать CLI локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Сведения об установке или обновлении Azure CLI см. в разделе [установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

        Для завершения изменения конфигурации может потребоваться несколько минут. По завершении появится сообщение, похожее на следующее и содержащее результат:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Выполните следующую команду с помощью Azure CLI:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Для завершения изменения конфигурации может потребоваться несколько минут. По завершении отобразится сообщение, похожее на следующее и содержащее результат:

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Включение с помощью шаблонов Azure Resource Manager
Мы создали пример Azure Resource Manager шаблоны для адаптации виртуальных машин и масштабируемых наборов виртуальных машин. Эти шаблоны включают сценарии, которые можно использовать для включения наблюдения за существующим ресурсом и создания нового ресурса с включенным наблюдением.

>[!NOTE]
>Шаблон необходимо развернуть в той же группе ресурсов, что и ресурс, который будет включен в доску.

Если вы не умеете развертывать ресурсы с помощью шаблона, см. следующие материалы:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Чтобы использовать Azure CLI, сначала необходимо установить и использовать CLI локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Сведения об установке или обновлении Azure CLI см. в разделе [установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Загрузить шаблоны

Шаблоны Azure Resource Manager предоставляются в файле архива (ZIP-файл), который можно [скачать](https://aka.ms/VmInsightsARMTemplates) из нашего репозитория GitHub. Содержимое файла включает папки, представляющие каждый сценарий развертывания с шаблоном и файлом параметров. Прежде чем запускать их, измените файл параметров и укажите необходимые значения. Не изменяйте файл шаблона, если его не нужно настраивать для поддержки конкретных требований. После изменения файла параметров его можно развернуть с помощью следующих методов, описанных далее в этой статье. 

Загружаемый файл содержит следующие шаблоны для различных сценариев.

- Шаблон **ексистингвмонбоардинг** позволяет Azure Monitor для виртуальных машин, если виртуальная машина уже существует.
- Шаблон **неввмонбоардинг** создает виртуальную машину и позволяет Azure Monitor для виртуальных машин отслеживать ее.
- Шаблон **ексистингвмссонбоардинг** позволяет Azure Monitor для виртуальных машин, если масштабируемый набор виртуальных машин уже существует.
- Шаблон **неввмссонбоардинг** создает масштабируемые наборы виртуальных машин и позволяет Azure Monitor для виртуальных машин отслеживать их.
- Шаблон **конфигуреворксапце** настраивает рабочую область log Analytics для поддержки Azure Monitor для виртуальных машин, включая решения и коллекцию счетчиков производительности операционных систем Linux и Windows.

>[!NOTE]
>Если масштабируемые наборы виртуальных машин уже присутствовали и для политики обновления задано значение **вручную**, Azure Monitor для виртуальных машин не будут включены для экземпляров по умолчанию после запуска шаблона Azure Resource Manager **ексистингвмссонбоардинг** . Экземпляры необходимо обновить вручную.

### <a name="deploy-by-using-azure-powershell"></a>Развертывание с помощью Azure PowerShell

Следующий шаг включает мониторинг с помощью Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Для завершения изменения конфигурации может потребоваться несколько минут. По завершении появится сообщение, похожее на следующее и содержащее результат:

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

Чтобы включить Azure Monitor для виртуальных машин для нескольких виртуальных машин или масштабируемых наборов виртуальных машин, используйте сценарий PowerShell [Инсталл-вминсигхтс. ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Он доступен из коллекции Azure PowerShell. Этот сценарий выполняет итерацию:

- Каждая виртуальная машина и масштабируемый набор виртуальных машин в вашей подписке.
- Группа ресурсов с областью действия, заданная параметром *ResourceGroup*. 
- Одна виртуальная машина или масштабируемый набор виртуальных машин, указанный по *имени*.

Для каждой виртуальной машины или масштабируемого набора виртуальных машин в сценарии проверяется, установлено ли расширение виртуальной машины. Если расширение виртуальной машины не установлено, сценарий попытается переустановить его. Если расширение виртуальной машины установлено, в сценарии устанавливается расширение виртуальной машины для агента Log Analytics и Dependency Agent.

Убедитесь, что используется модуль Azure PowerShell AZ Version 1.0.0 или более поздней версии с `Enable-AzureRM` включенными псевдонимами совместимости. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). При использовании PowerShell на локальном компьютере также нужно запустить `Connect-AzAccount`, чтобы создать подключение к Azure.

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

## <a name="next-steps"></a>Следующие шаги

Теперь, когда наблюдение включено для виртуальных машин, эти сведения доступны для анализа с Azure Monitor для виртуальных машин.
 
- Чтобы узнать, как использовать функцию работоспособности, см. статью [просмотр Azure Monitor для виртуальных машин работоспособности](vminsights-health.md). 
- Для просмотра обнаруженных зависимостей приложений см. статью о [просмотре схемы Azure Monitor для виртуальных машин](vminsights-maps.md). 
- Чтобы определить узкие места и общее использование с производительностью виртуальной машины, см. статью [Просмотр производительности виртуальной машины Azure](vminsights-performance.md). 
- Для просмотра обнаруженных зависимостей приложений см. статью о [просмотре схемы Azure Monitor для виртуальных машин](vminsights-maps.md).