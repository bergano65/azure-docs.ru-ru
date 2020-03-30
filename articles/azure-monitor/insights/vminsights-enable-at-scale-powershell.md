---
title: Включить Azure Monitor для VMs с PowerShell или шаблонами
description: В этой статье описывается, как вы используете Azure Monitor для виртуальных компьютеров для одного или нескольких виртуальных машин или виртуальных наборов масштабов машин с помощью шаблонов Azure PowerShell или Azure Resource Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 75d5203e7c475a44b6a00dbf9286f43114b7b54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480850"
---
# <a name="enable-azure-monitor-for-vms-using-azure-powershell-or-resource-manager-templates"></a>Включите мониторинг Azure для vMs с помощью шаблонов Azure PowerShell или менеджера ресурсов

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

В этой статье объясняется, как включить Azure Monitor для виртуальных компьютеров Azure или виртуальных наборов масштабов машин с помощью шаблонов Azure PowerShell или Azure Resource Manager. В конце этого процесса, вы успешно начали мониторинг всех ваших виртуальных машин и узнать, если какие-либо испытывают проблемы с производительностью или доступностью.

## <a name="set-up-a-log-analytics-workspace"></a>Настройка рабочей области Log Analytics

Если у вас нет рабочего пространства Log Analytics, необходимо создать его. Просмотрите методы, предлагаемые в разделе [«Предпосылки»,](vminsights-enable-overview.md#log-analytics) прежде чем продолжить шаги по его настройке. Затем можно завершить развертывание Azure Monitor для вс-бай-а до внедрение с помощью шаблона шаблона Azure Resource Manager.

### <a name="install-the-vminsights-solution"></a>Установка решения VMInsights

В этом методе используется шаблон JSON, который задает конфигурацию для включения компонентов решения в рабочей области Log Analytics.

Если вы не знаете, как развертывать ресурсы с помощью шаблона, см.:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Чтобы использовать Azure CLI, сначала необходимо установить и использовать CLI локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Для установки или обновления Azure CLI [см.](https://docs.microsoft.com/cli/azure/install-azure-cli)

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
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
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

        Изменение конфигурации может занять несколько минут. Когда оно закончено, сообщение отображается, что похоже на следующее и включает в себя результат:

        ```output
        provisioningState       : Succeeded
        ```

    * Выполните следующую команду с помощью Azure CLI:

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Изменение конфигурации может занять несколько минут. Когда оно закончено, отображается сообщение, аналогичное следующему и включает результат:

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Включить шаблоны управления ресурсами Azure

Мы создали пример шаблонов Azure Resource Manager для посадки виртуальных машин и виртуальных наборов машин. Эти шаблоны включают сценарии, которые можно использовать для включения мониторинга на существующем ресурсе и создания нового ресурса с включенным мониторингом.

>[!NOTE]
>Шаблон должен быть развернут в той же группе ресурсов, что и ресурс, который должен быть приведен в соответствие.

Если вы не знаете, как развертывать ресурсы с помощью шаблона, см.:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Чтобы использовать Azure CLI, сначала необходимо установить и использовать CLI локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Для установки или обновления Azure CLI [см.](https://docs.microsoft.com/cli/azure/install-azure-cli)

### <a name="download-templates"></a>Скачать шаблоны

Шаблоны менеджера ресурсов Azure предоставляются в архивном файле (.zip), который можно [загрузить](https://aka.ms/VmInsightsARMTemplates) из нашего репо GitHub. Содержимое файла включает папки, представляющие каждый сценарий развертывания с файлом шаблона и параметра. Перед запуском изыск нисбы параметры и укажите требуемые значения. Не изменяйте файл шаблона, если вам не нужно настроить его для поддержки ваших конкретных требований. После изменения файла параметра можно развернуть его, используя следующие методы, описанные позднее в этой статье.

Файл загрузки содержит следующие шаблоны для различных сценариев:

- **Существующий шаблонVmOnboarding** позволяет Azure Monitor для виртуальных вывеши, если виртуальная машина уже существует.
- **Шаблон NewVmOnboarding** создает виртуальную машину и позволяет Azure Monitor для виртуальных компьютеров контролировать ее.
- **Существующий шаблонVmssOnboarding** позволяет Azure Monitor для виртуальных вывеши, если набор виртуальной шкалы машин уже существует.
- **Шаблон NewVmssOnboarding** создает виртуальные наборы масштабов машин и позволяет Azure Monitor для виртуальных вывешидляемых вм: контролировать их.
- **Шаблон ConfigureWorkspace** настраивает рабочее пространство Log Analytics для поддержки Azure Monitor для вс-кодов, позволяя создавать решения и создавать счетчики производительности операционной системы Linux и Windows.

>[!NOTE]
>Если наборы виртуальных масштабов машин уже присутствуют и политика обновления настроена на **Руководство,** Azure Monitor для виртуальных технологий не будет включен для экземпляров по умолчанию после запуска шаблона **ExistingVmssOnboarding Andboarding** Resource Manager. Вы должны вручную обновить экземпляры.

### <a name="deploy-by-using-azure-powershell"></a>Развертывание с помощью Azure PowerShell

Следующий шаг позволяет осуществлять мониторинг с помощью Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Изменение конфигурации может занять несколько минут. Когда оно закончено, сообщение отображается, что похоже на следующее и включает в себя результат:

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Развертывание с помощью Azure CLI

Следующий шаг позволяет осуществлять мониторинг с помощью Azure CLI.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Результат выглядит следующим образом:

```output
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Включение с помощью PowerShell

Для включения Azure Monitor для виртуальных виртуальных технологий для нескольких виртуальных компьютеров или виртуальных наборов машин используйте скрипт PowerShell [Install-VMInsights.ps1.](https://www.powershellgallery.com/packages/Install-VMInsights/1.0) Он доступен в галерее Azure PowerShell. Этот скрипт итерирует через:

- Каждая виртуальная машина и виртуальная машина масштабируется в вашей подписке.
- Группа ресурсов, указанная *ResourceGroup.*
- Один набор VM или виртуальной машины масштаба, который указан в *name.*

Для каждой виртуальной машины или масштабируемого набора виртуальных машин в сценарии проверяется, установлено ли расширение виртуальной машины. Если удлинитель VM установлен, скрипт пытается переустановить его. Если расширение VM не установлено, скрипт устанавливает расширение VM-агента по анализу журналов и зависимости.

Проверьте, вы используете модуль Azure PowerShell Az `Enable-AzureRM` версии 1.0.0 или позже с включенными псевдонимами совместимости. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). При использовании PowerShell на локальном компьютере также нужно запустить `Connect-AzAccount`, чтобы создать подключение к Azure.

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

Теперь, когда мониторинг включен для ваших виртуальных машин, эта информация доступна для анализа с Azure Monitor для виртуальных технологий.

- Для просмотра обнаруженных зависимостей приложений см. статью о [просмотре схемы Azure Monitor для виртуальных машин](vminsights-maps.md).

- Чтобы определить узкие места и общее использование с производительностью VM, [см.](vminsights-performance.md)
