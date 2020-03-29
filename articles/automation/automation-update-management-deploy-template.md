---
title: Используйте шаблоны менеджера ресурсов Azure для управления обновлениями на борту Документы Майкрософт
description: Шаблон менеджера ресурсов Azure можно использовать для решения azure Automation Update Management.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/27/2020
ms.openlocfilehash: a8b382663b56d7481da876979e33194fb0ac533d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925804"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Решение «Управление бортом обновления» с использованием шаблона управления ресурсами Azure

Шаблоны [Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) можно использовать для включения решения управления обновлением Azure Automation в группу ресурсов. В этой статье приводится пример шаблона, который автоматизирует следующее:

* Создание рабочего пространства анализа журналов Azure Monitor Analytics.
* Создание учетной записи Azure Automation.
* Ссылки на учетную запись Автоматизации с рабочим пространством Log Analytics, если они еще не связаны.
* На борту решения по управлению обновлением автоматизации Azure

Шаблон не автоматизировать посадку одного или нескольких VMs Azure или не-Azure.

Если в подписке уже развернута рабочая область и учетная запись Автоматизации Log Analytics и Автоматизация в поддерживаемом регионе, они не связаны между собой, а рабочее пространство уже не развертывается с решением управления обновлением, используя этот шаблон, который успешно создает этот шаблон связь и развертывает решение управления обновлением. 

## <a name="api-versions"></a>Версии API

В следующей таблице перечислены версии API для ресурсов, используемых в этом примере.

| Ресурс | Тип ресурса | Версия API |
|:---|:---|:---|
| Рабочая область | workspaces | 2017-03-15-preview |
| Учетная запись службы автоматизации | служба автоматизации | 2015-10-31 | 
| Решение | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Перед использованием шаблона

Если вы решите установить и использовать PowerShell локально, эта статья требует модуль Azure PowerShell Az. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если необходимо выполнить обновление, см. статью об [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure. С Azure PowerShell развертывание использует [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment)

Если вы решили установить и использовать CLI локально, эта статья требует, чтобы вы запускали версию Azure CLI 2.1.0 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) С Azure CLI это развертывание использует [создание группы az.](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) 

Шаблон JSON настроен, чтобы подсказать вам:

* Название рабочей области
* Регион для создания рабочего пространства в
* Название учетной записи Автоматизация
* Регион для создания учетной записи в

Шаблон JSON определяет значение по умолчанию для других параметров, которые, вероятно, будут использоваться в качестве стандартной конфигурации в вашей среде. Шаблон можно хранить в учетной записи хранения Azure для общего доступа в организации. Для получения дополнительной информации о работе с шаблонами см. [Ресурсы Развертывание с шаблонами «Менеджер ресурсов» и Azure CLI.](../azure-resource-manager/templates/deploy-cli.md)

Следующие параметры в шаблоне устанавливаются со значением по умолчанию для рабочего пространства Log Analytics:

* sku — по умолчанию используется новый тарифный план с платой за гигабайт, выпущенный в апреле 2018 года.
* хранение данных - по умолчанию до тридцати дней

>[!WARNING]
>При создании или настройке рабочей области Log Analytics в подписке, использующей модель ценообразования от апреля 2018 года, доступна только ценовая категория **PerGB2018**.
>

>[!NOTE]
>Прежде чем использовать этот шаблон, просмотрите [дополнительные детали,](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) чтобы полностью понять параметры конфигурации рабочего пространства, такие как режим управления доступом, уровень ценообразования, сохранение и уровень резервирования емкости. Если вы новичок в журналах Azure Monitor и уже не развернули рабочее пространство, следует ознакомиться с руководством по [проектированию рабочего пространства,](../azure-monitor/platform/design-logs-deployment.md) чтобы узнать о контроле доступа, а также понимание стратегий реализации проекта, которые мы рекомендуем для вашей организации.

## <a name="deploy-template"></a>Развертывание шаблона

1. Скопируйте и вставьте в него следующий синтаксис JSON:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "pricingTier": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": { 
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
      ]
    }
    ```

2. Отредактируйте шаблон с учетом ваших требований.

3. Сохранить этот файл как deployUMSolutiontemplate.json в локальной папке.

4. Теперь вы можете развернуть этот шаблон. Вы можете использовать либо PowerShell, либо Azure CLI. Когда вам будет предложено учесть рабочее пространство и имя учетной записи Automation, укажите имя, которое является уникальным во всем мире во всех подписках Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Лазурный CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Развертывание может занять несколько минут. По завершении выполнения появится сообщение с результатами наподобие приведенного ниже.

    ![Пример результатов по завершении развертывания](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда у вас развернуто решение управления обновлением, можно включить ввм-мы для управления, просмотреть оценки обновлений и развернуть обновления, чтобы привести их в соответствие.

- В [Azure Automation приходится](automation-onboard-solutions-from-automation-account.md) на одну или несколько машин Azure и вручную для машин, не относясь к Azure.

- Для одного Azure VM со страницы виртуальной машины на портале Azure. Этот сценарий доступен для [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) и [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) VMs.

- Для [нескольких виртуальных проектов Azure,](manage-update-multi.md) выбрав их со **страницы виртуальных машин** на портале Azure. 