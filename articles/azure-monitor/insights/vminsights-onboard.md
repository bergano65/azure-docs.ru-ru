---
title: Развертывание Azure Monitor для виртуальных машин (предварительная версия) | Документация Майкрософт
description: В этой статье объясняется, как развернуть и настроить Azure Monitor для виртуальных машин, чтобы получать сведения о производительности распределенного приложения и обнаруживать проблемы работоспособности.
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
ms.date: 02/01/2019
ms.author: magoedte
ms.openlocfilehash: 37e2da00ecbecdddcc8757e64c57b03774092e14
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814995"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Развертывание Azure Monitor для виртуальных машин (предварительная версия)
В этой статье описана настройка Azure Monitor для виртуальных машин. Служба отслеживает работоспособность операционной системы виртуальных машин и масштабируемых наборов виртуальных машин Azure, а также виртуальных машин в вашей среде. Этот мониторинг включает обнаружение и сопоставление зависимостей приложений, которые можно разместить на машинах. 

Azure Monitor для виртуальных машин включается одним из следующих способов:

* Включите одну виртуальную машину Azure, выбрав страницу **Аналитические сведения (предварительная версия)** непосредственно на виртуальной машине.
* Включите две или несколько виртуальных машин Azure с помощью службы "Политика Azure". Этим способом устанавливаются и настраиваются необходимые зависимости существующих и новых виртуальных машин. Вы получите отчеты о всех виртуальных машинах, которые не соответствуют требованиям, что позволит вам принять решение о том, следует ли включить их и как их нужно исправить.
* Включите две или несколько виртуальных машин или масштабируемых наборов виртуальных машин Azure в определенной подписке или группе ресурсов с помощью PowerShell.

Дополнительные сведения о каждом способе приведены ниже в этой статье.

## <a name="prerequisites"></a>Предварительные требования
Прежде чем начинать работу, убедитесь, что вы понимаете сведения, содержащиеся в следующих разделах.

### <a name="log-analytics"></a>Log Analytics

Сейчас рабочая область Log Analytics поддерживается в следующих регионах:

- Западно-центральная часть США
- Восточная часть США
- Западная Европа
- Юго-Восточная Азия<sup>1</sup>

<sup>1</sup> Этот регион сейчас не поддерживает функцию мониторинга работоспособности в Azure Monitor для виртуальных машин.

>[!NOTE]
>Виртуальные машины Azure можно развернуть из любого региона, а не только из поддерживаемых регионов для рабочей области Log Analytics.
>

Если у вас нет рабочей области, можно создать ее одним из следующих способов:
* [CLI Azure.](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Портал Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Если включить мониторинг для одной виртуальной машины Azure на портале Azure, можно создать рабочую область во время этого процесса.

Чтобы использовать решение для сценария масштабирования, сначала необходимо внести следующие настройки в рабочей области Log Analytics:

* Установите решения ServiceMap и InfrastructureInsights. Установку можно выполнить только с помощью шаблона Azure Resource Manager, который предоставляется в этой статье.
* Настройте рабочую область Log Analytics для сбора данных счетчиков производительности.

Чтобы настроить рабочую область Log Analytics для этого сценария масштабирования, ознакомьтесь с соответствующими инструкциями.

### <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

В таблице ниже перечислены версии операционных систем Windows и Linux, которые поддерживает Azure Monitor для виртуальных машин. Полный список с описанием основных и дополнительных выпусков ОС Linux и поддерживаемых версий ядра приводится далее в этом разделе.

|Версия ОС |Производительность |Карты |Health |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X |  |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 7, 6| X | X| X |
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |
|CentOS Linux 7, 6 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Oracle Linux 7 | X<sup>1</sup> | | X |
|Oracle Linux 6 | X | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | | X |

<sup>1</sup> Функция "Производительность" Azure Monitor для виртуальных машин доступна только в Azure Monitor. К ней нельзя получить доступ непосредственно с левой панели виртуальной машины Azure.

>[!NOTE]
>Следующие сведения относятся к поддержке ОС Linux:
> - Поддерживаются только версии ядра по умолчанию и SMP для Linux.
> - Нестандартные версии ядра, такие как расширение физических адресов (PAE) и Xen, не поддерживаются ни в одном дистрибутиве Linux. Например, система со строкой версии *2.6.16.21-0.8-xen* не поддерживается.
> - Пользовательские ядра, включая повторные компиляции стандартных ядер, не поддерживаются.
> - Ядро CentOSPlus не поддерживается.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Версия ОС | Версия ядра |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1. | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0–514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Версия ОС | Версия ядра |
|:--|:--|
| 6,0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6,5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="ubuntu-server"></a>Сервер Ubuntu

| Версия ОС | Версия ядра |
|:--|:--|
| Ubuntu 18.04 | ядро 4.15.* |
| Ubuntu 16.04.3 | ядро 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 с Unbreakable Enterprise Kernel
| Версия ОС | Версия ядра
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6,5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 с Unbreakable Enterprise Kernel

| Версия ОС | Версия ядра
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Версия ОС | Версия ядра
|:--|:--|
|12 с пакетом обновления 2 | 4.4.* |
|12 с пакетом обновления 3 | 4.4.* |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent
Функция схемы в Azure Monitor для виртуальных машин получает данные от Microsoft Dependency Agent. Dependency Agent является зависимым от агента Log Analytics для создания подключений к Log Analytics. Таким образом, в системе нужно установить агент Log Analytics и настроить для него Dependency Agent.

При включении решения "Azure Monitor для виртуальных машин" для отдельной виртуальной машины или при использовании методов для масштабируемого развертывания необходимо с помощью расширения Dependency Agent для виртуальных машин Azure установить агент в рамках этого процесса.

В гибридной среде можно загрузить и установить Dependency Agent двумя способами: вручную или с помощью метода автоматического развертывания для виртуальных машин, размещенных за пределами Azure.

В приведенной ниже таблице описаны подключенные источники, которые функция схемы поддерживает в гибридной среде.

| Подключенный источник | Поддерживаются | ОПИСАНИЕ |
|:--|:--|:--|
| Агенты Windows | Yes | Кроме [агента Log Analytics для Windows](../../azure-monitor/platform/log-analytics-agent.md) агентам Windows требуется Microsoft Dependency Agent. Полный список версий операционных систем см. в разделе [Поддерживаемые операционные системы](#supported-operating-systems). |
| Агенты Linux | Yes | Кроме [агента Log Analytics для Linux](../../azure-monitor/platform/log-analytics-agent.md) агентам Linux требуется Microsoft Dependency Agent. Полный список версий операционных систем см. в разделе [Поддерживаемые операционные системы](#supported-operating-systems). |
| Группа управления System Center Operations Manager | Нет  | |

Dependency Agent можно скачать из следующего расположения:

| Файл | ОС | Version (версия) | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) |  Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>Контроль доступа на основе ролей
Чтобы включить функции Azure Monitor для виртуальных машин и получить к ним доступ, нужно назначить следующие роли доступа:

- Чтобы включить решение, у вас должна быть роль *Участник Log Analytics*.

- Для просмотра данных о производительности, работоспособности и схеме у вас должна быть роль *Читатель данных мониторинга* для виртуальной машины Azure. Необходимо настроить рабочую область Log Analytics для Azure Monitor для виртуальных машин.

Дополнительные сведения о том, как управлять доступом к рабочей области Log Analytics, см. в статье [Управление рабочими областями](../../azure-monitor/platform/manage-access.md).

## <a name="enable-monitoring-in-the-azure-portal"></a>Включение мониторинга на портале Azure
Чтобы включить мониторинг виртуальной машины на портале Azure, сделайте следующее.

1. Войдите на [портале Azure](https://portal.azure.com).

1. Выберите **Виртуальные машины**.

1. Выберите виртуальную машину из списка.

1. На странице виртуальной машины в разделе **Мониторинг** выберите **Аналитические сведения (предварительная версия)**.

1. На странице **Аналитические сведения (предварительная версия)** выберите **Попробовать**.

    ![Включение Azure Monitor для виртуальной машины](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)
1. Если имеющаяся рабочая область Log Analytics расположена в той же подписке, что и кластер, выберите ее из раскрывающегося списка на странице **Azure Monitor Insights Onboarding** (Подключение к службе аналитических сведений Azure Monitor).  
    В списке предварительно выбрана рабочая область по умолчанию и расположение, в которое развертывается виртуальная машина в подписке. 

    >[!NOTE]
    >Если вы хотите создать новую рабочую область Log Analytics для хранения данных мониторинга от виртуальной машины, в одном из приведенных выше поддерживаемых регионов выполните инструкции из статьи [Создание рабочей области Log Analytics на портале Azure](../../azure-monitor/learn/quick-create-workspace.md).

После включения мониторинга может потребоваться около 10 минут, прежде чем вы сможете просмотреть метрики работоспособности для виртуальной машины.

![Включение Azure Monitor для обработки развертывания мониторинга виртуальных машин](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>Развертывание в масштабе
В рамках этого раздела вы развернете Azure Monitor для виртуальных машин в нужном масштабе с помощью Политики Azure или Azure PowerShell.

Перед развертыванием виртуальных машин предварительно настройте рабочую область Log Analytics, сделав следующее:

1. Если у вас еще нет рабочего пространства, создайте рабочее пространство, которое может поддерживать Azure Monitor для виртуальных машин.  
    Прежде чем продолжить, см. статью [Manage workspaces](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) (Управление рабочими областями), где приведены рекомендации по стоимости, управлению и соответствию требованиям.

1. Создание рабочей области (если она еще не существует), которая может использоваться для поддержки Azure Monitor для виртуальных машин. Прежде чем создать рабочую область, ознакомьтесь со статьей [Управление рабочими областями](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json), чтобы узнать о связанных затратах, методах управления и рекомендациях по соответствию требованиям.

1. Включение счетчиков производительности в рабочей области для сбора данных на виртуальных машинах Windows и Linux.

1. Установите и включите решения ServiceMap и InfrastructureInsights в рабочей области.

### <a name="set-up-a-log-analytics-workspace"></a>Настройка рабочей области Log Analytics
Если у вас нет рабочей области Log Analytics, создайте ее, просмотрев методы, предложенные в разделе ["Предварительные требования"](#log-analytics).

#### <a name="enable-performance-counters"></a>Включение счетчиков производительности
Если указанная в решении рабочая область Log Analytics еще не настроена для сбора данных от необходимых решению счетчиков производительности, их необходимо включить отдельно. Это можно сделать одним из двух способов:
* вручную, как описано в статье [Источники данных о производительности Windows и Linux в Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md);
* загрузив и запустив сценарий PowerShell, доступный в [коллекции Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).

#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Установите решения ServiceMap и InfrastructureInsights.
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
        New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
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

### Enable by using Azure Policy
To enable Azure Monitor for VMs at scale in a way that helps ensure consistent compliance and the automatic enabling of the newly provisioned VMs, we recommend [Azure Policy](../../azure-policy/azure-policy-introduction.md). These policies:

* Deploy the Log Analytics agent and the Dependency agent.
* Report on compliance results.
* Remediate for non-compliant VMs.

To enable Azure Monitor for VMs by using Azure Policy in your tenant:

- Assign the initiative to a scope: management group, subscription, or resource group
- Review and remediate compliance results

For more information about assigning Azure Policy, see [Azure Policy overview](../../governance/policy/overview.md#policy-assignment) and review the [overview of management groups](../../governance/management-groups/index.md) before you continue.

The policy definitions are listed in the following table:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Enable Azure Monitor for VMs |Enable Azure Monitor for the Virtual Machines (VMs) in the specified scope (management group, subscription, or resource group). Takes Log Analytics workspace as a parameter. |Initiative |
|[Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Linux VMs |Deploy Dependency Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Windows VMs |Deploy Dependency Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Linux VMs |Deploy Log Analytics Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Windows VMs |Deploy Log Analytics Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |

Standalone policy (not included with the initiative) is described here:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Audit Log Analytics Workspace for VM - Report Mismatch |Report VMs as non-compliant if they aren't logging to the Log Analytics workspace specified in the policy/initiative assignment. |Policy |

#### Assign the Azure Monitor initiative
With this initial release, you can create the policy assignment only in the Azure portal. To understand how to complete these steps, see [Create a policy assignment from the Azure portal](../../governance/policy/assign-policy-portal.md).

1. To launch the Azure Policy service in the Azure portal, select **All services**, and then search for and select **Policy**.

1. In the left pane of the Azure Policy page, select **Assignments**.  
    An assignment is a policy that has been assigned to take place within a specific scope.
    
1. At the top of the **Policy - Assignments** page, select **Assign Initiative**.

1. On the **Assign Initiative** page, select the **Scope** by clicking the ellipsis (...), and select a management group or subscription.  
    In our example, a scope limits the policy assignment to a grouping of virtual machines for enforcement.
    
1. At the bottom of the **Scope** page, save your changes by selecting **Select**.

1. (Optional) To remove one or more resources from the scope, select **Exclusions**.

1. Select the **Initiative definition** ellipsis (...) to display the list of available definitions, select **[Preview] Enable Azure Monitor for VMs**, and then select **Select**.  
    The **Assignment name** box is automatically populated with the initiative name you selected, but you can change it. You can also add an optional description. The **Assigned by** box is automatically populated based on who is logged in, and this value is optional.
    
1. In the **Log Analytics workspace** drop-down list for the supported region, select a workspace.

    >[!NOTE]
    >If the workspace is beyond the scope of the assignment, grant *Log Analytics Contributor* permissions to the policy assignment's Principal ID. If you don't do this, you might see a deployment failure such as: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... `
    >To grant access, review [how to manually configure the managed identity](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
    >  
    The **Managed Identity** check box is selected, because the initiative being assigned includes a policy with the *deployIfNotExists* effect.
    
1. In the **Manage Identity location** drop-down list, select the appropriate region.

1. Select **Assign**.

#### Review and remediate the compliance results

You can learn how to review compliance results by reading [identify non-compliance results](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). In the left pane, select **Compliance**, and then locate the **[Preview] Enable Azure Monitor for VMs** initiative for VMs that aren't compliant according to the assignment you created.

![Policy compliance for Azure VMs](./media/vminsights-onboard/policy-view-compliance-01.png)

Based on the results of the policies included with the initiative, VMs are reported as non-compliant in the following scenarios:

* Log Analytics or the Dependency agent isn't deployed.  
    This scenario is typical for a scope with existing VMs. To mitigate it, deploy the required agents by [creating remediation tasks](../../governance/policy/how-to/remediate-resources.md) on a non-compliant policy.  
    - [Preview]: Deploy Dependency Agent for Linux VMs
    - [Preview]: Deploy Dependency Agent for Windows VMs
    - [Preview]: Deploy Log Analytics Agent for Linux VMs
    - [Preview]: Deploy Log Analytics Agent for Windows VMs

* VM Image (OS) isn't identified in the policy definition.  
    The criteria of the deployment policy include only VMs that are deployed from well-known Azure VM images. Check the documentation to see whether the VM OS is supported. If it isn't supported, duplicate the deployment policy and update or modify it to make the image compliant.  
    - [Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted
    - [Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted

* VMs aren't logging in to the specified Log Analytics workspace.  
    It's possible that some VMs in the initiative scope are logging in to a Log Analytics workspace other than the one that's specified in the policy assignment. This policy is a tool to identify which VMs are reporting to a non-compliant workspace.  
    - [Preview]: Audit Log Analytics Workspace for VM - Report Mismatch

### Enable with PowerShell
To enable Azure Monitor for VMs for multiple VMs or virtual machine scale sets, you can use the PowerShell script [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), available from the Azure PowerShell Gallery. This script iterates through every virtual machine and virtual machine scale set in your subscription, in the scoped resource group that's specified by *ResourceGroup*, or to a single VM or virtual machine scale set that's specified by *Name*. For each VM or virtual machine scale set, the script verifies whether the VM extension is already installed. If the VM extension is not installed, the script tries to reinstall it. If the VM extension is installed, the script installs the Log Analytics and Dependency agent VM extensions.

This script requires Azure PowerShell module version 5.7.0 or later. Run `Get-Module -ListAvailable AzureRM` to find the version. If you need to upgrade, see [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). If you're running PowerShell locally, you also need to run `Connect-AzureRmAccount` to create a connection with Azure.

To get a list of the script's argument details and example usage, run `Get-Help`.

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

## <a name="enable-for-a-hybrid-environment"></a>Включение в гибридной среде
В этом разделе объясняется, как развернуть виртуальные машины или физические компьютеры, размещенные в вашем центре обработки данных или других облачных средах, для мониторинга средствами Azure Monitor для виртуальных машин.

Dependency Agent в Azure Monitor для виртуальных машин не передает данные и не требует изменений в настройках брандмауэра или портов. Данные сопоставления всегда передаются агентом Log Analytics в службу Azure Monitor либо непосредственно, либо через [шлюз OMS](../../azure-monitor/platform/gateway.md), если политики безопасности ИТ не позволяют компьютерам в сети подключаться к Интернету.

Ознакомьтесь с требованиями и методами развертывания для [агента Log Analytics в Linux и Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Инструкции кратко перечислены ниже:

1. Установите агент Log Analytics для Windows или Linux.

1. Скачайте и установите Dependency Agent схемы Azure Monitor для виртуальных машин [Windows](https://aka.ms/dependencyagentwindows) или [Linux](https://aka.ms/dependencyagentlinux).

1. Включите сбор данных счетчиков производительности.

1. Разверните Azure Monitor для виртуальных машин.

### <a name="install-the-dependency-agent-on-windows"></a>Установка Dependency Agent на Windows
Dependency Agent можно вручную установить на компьютерах Windows с помощью `InstallDependencyAgent-Windows.exe`. Если запустить этот исполняемый файл без параметров, запустится мастер для установки агента в интерактивном режиме.

>[!NOTE]
>Для установки или удаления агента требуются права *администратора*.

В приведенной ниже таблице выделены параметры, поддерживаемые при установке агента из командной строки.

| Параметр | ОПИСАНИЕ |
|:--|:--|
| /? | Возвращает список параметров командной строки. |
| /S | Автоматическая установка без взаимодействия с пользователем. |

Например, чтобы запустить программу установки с параметром `/?`, введите **InstallDependencyAgent-Windows.exe /?**.

Файлы для Windows Dependency Agent по умолчанию устанавливаются в папку *C:\Program Files\Microsoft Dependency Agent*. Если Dependency Agent не запускается после завершения установки, просмотрите подробные сведения об ошибке в записях журналов. Каталогом журналов является *%Programfiles%\Microsoft Dependency Agent\logs*.

### <a name="install-the-dependency-agent-on-linux"></a>Установка Dependency Agent в Linux
Dependency Agent устанавливается на серверах Linux с помощью файла *InstallDependencyAgent-Linux64.bin*. Это сценарий оболочки с самоизвлекающимся двоичным файлом. Вы можете запустить этот файл с помощью `sh` или добавить разрешения на выполнение в сам файл.

>[!NOTE]
> Для установки или настройки агента требуется доступ с правами привилегированного пользователя.
>

| Параметр | ОПИСАНИЕ |
|:--|:--|
| -help | Получение списка параметров командной строки. |
| -s | Выполняет автоматическую установку без вывода сообщений для пользователя. |
| --check | Проверка разрешений и операционной системы без установки агента. |

Например, чтобы запустить программу установки с параметром `-help`, введите **InstallDependencyAgent-Linux64.bin -help**.

Установите Dependency Agent для Linux с правами привилегированного пользователя. Для этого выполните команду `sh InstallDependencyAgent-Linux64.bin`.

Если Dependency Agent не запускается, просмотрите подробные сведения об ошибке в записях журналов. В агентах Linux каталог журнала находится в расположении */var/opt/microsoft/dependency-agent/log*.

Файлы для Dependency Agent размещаются в следующих каталогах:

| Файлы | Расположение |
|:--|:--|
| Основные файлы | /opt/microsoft/dependency-agent |
| Файлы журналов | /var/opt/microsoft/dependency-agent/log |
| Файлы конфигурации | /etc/opt/microsoft/dependency-agent/config |
| Исполняемые файлы службы | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Двоичные файлы хранилища | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Включение счетчиков производительности
Если указанная в решении рабочая область Log Analytics еще не настроена для сбора данных от необходимых решению счетчиков производительности, их нужно будет включить отдельно. Это можно сделать одним из двух способов:
* вручную, как описано в статье [Источники данных о производительности Windows и Linux в Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md);
* загрузив и запустив сценарий PowerShell, доступный в [коллекции Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).

### <a name="deploy-azure-monitor-for-vms"></a>Развертывание Azure Monitor для виртуальных машин
В этом методе используется шаблон JSON, который задает конфигурацию для включения компонентов решения в рабочей области Log Analytics.

Если вы не знакомы с развертыванием ресурсов с помощью шаблона, см. статьи:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

#### <a name="create-and-execute-a-template"></a>Создание и выполнение шаблона

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

1. Измените значения параметров *WorkspaceName*, *ResourceGroupName* и *WorkspaceLocation*. Значение *WorkspaceName* — это полный идентификатор ресурса рабочей области Log Analytics, включая имя рабочей области. Значение *WorkspaceLocation* — регион, в котором определена рабочая область.

1. Теперь все готово для развертывания этого шаблона с помощью следующей команды PowerShell:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Изменение конфигурации может занять несколько минут. После настройки появится сообщение, похожее на приведенное ниже, с таким результатом:

    ```powershell
    provisioningState       : Succeeded
    ```
После включения мониторинга может потребоваться около 10 минут, прежде чем вы сможете просмотреть сведения о состоянии работоспособности и метрики для гибридного компьютера.

## <a name="performance-counters-enabled"></a>Включение счетчиков производительности
Azure Monitor для виртуальных машин настраивает сбор данных от счетчиков производительности, используемых решением, в рабочей области Log Analytics. В следующей таблице перечислены объекты и счетчики, которые настраиваются решением и опрашиваются каждые 60 секунд.

### <a name="windows-performance-counters"></a>Счетчики производительности Windows

|Имя объекта |Имя счетчика |
|------------|-------------|
|Логический диск |Процент свободного места |
|Логический диск |Среднее время чтения с диска (с) |
|Логический диск |Среднее время обращения к диску (с) |
|Логический диск |Среднее время записи на диск (с) |
|Логический диск |Байт в секунду для диска |
|Логический диск |Скорость чтения с диска (байт/с)  |
|Логический диск |Операций чтения с диска в секунду  |
|Логический диск |Обращений к диску в секунду |
|Логический диск | Скорость записи на диск (байт/с) |
|Логический диск | Операций записи на диск в секунду |
|Логический диск |Свободно мегабайт |
|Память |Доступный объем в МБ |
|Сетевой адаптер |Полученных байтов/с |
|Сетевой адаптер |Отправленных байтов/с |
|Процессор |% загруженности процессора |

### <a name="linux-performance-counters"></a>Счетчики производительности Linux

|Имя объекта |Имя счетчика |
|------------|-------------|
|Логический диск |Процент используемого места |
|Логический диск |Скорость чтения с диска (байт/с)  |
|Логический диск |Операций чтения с диска в секунду  |
|Логический диск |Обращений к диску в секунду |
|Логический диск | Скорость записи на диск (байт/с) |
|Логический диск | Операций записи на диск в секунду |
|Логический диск |Свободно мегабайт |
|Логический диск |Скорость обмена с логическим диском (байт/с) |
|Память |Доступный объем памяти в МБ |
|Сеть |Всего получено байт |
|Сеть |Всего передано байт |
|Процессор |% загруженности процессора |

## <a name="diagnostic-and-usage-data"></a>Данные диагностики и использования
Корпорация Майкрософт автоматически собирает данные об использовании и производительности при работе службы Azure Monitor. Эти данные используются, чтобы поддерживать и улучшать качество, безопасность и целостность службы. Чтобы предоставить возможности для точного и эффективного устранения неполадок, данные функции схемы включают в себя сведения о конфигурации программного обеспечения, такие как версия операционной системы, а также IP-адрес, DNS-имя и имя рабочей станции. Корпорация Майкрософт не собирает сведения об именах, адресах и другую контактную информацию.

Дополнительные сведения о сборе и использовании данных см. в [заявлении о конфиденциальности служб Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>Дополнительная информация

Теперь, когда мониторинг для виртуальной машины включен, эта информация доступна для анализа в Azure Monitor для виртуальных машин. Чтобы узнать, как использовать функцию работоспособности, см. статью о [просмотре сведений о работоспособности Azure Monitor для виртуальных машин](vminsights-health.md). Для просмотра обнаруженных зависимостей приложений см. статью о [просмотре схемы Azure Monitor для виртуальных машин](vminsights-maps.md).
