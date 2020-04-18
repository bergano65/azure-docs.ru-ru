---
title: Используйте шаблоны менеджера ресурсов Azure для управления обновлениями на борту Документы Майкрософт
description: Шаблон менеджера ресурсов Azure можно использовать для решения azure Automation Update Management.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/30/2020
ms.openlocfilehash: 81f9d242d93ffe513c0c3733ceb9d38ca9cadc1c
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617459"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Решение «Управление бортом обновления» с использованием шаблона управления ресурсами Azure

Шаблоны [Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) можно использовать для включения решения управления обновлением Azure Automation в группу ресурсов. В этой статье приводится пример шаблона, который автоматизирует следующее:

* Создание рабочего пространства анализа журналов Azure Monitor Analytics.
* Создание учетной записи Azure Automation.
* Если если не связано с учетной записью Автоматизация, то это рабочее пространство Log Analytics.
* Решение по управлению обновлением автоматизации Azure.

Шаблон не автоматизировать посадку одного или нескольких VMs Azure или не-Azure.

Если в подписке у вас уже есть рабочая область и учетная запись «Автоматизация журнала», они не связаны между собой. В рабочей области уже нет развертывания решения управления обновлением. Использование этого шаблона успешно создает ссылку и развертывает решение управления обновлением. 

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="api-versions"></a>Версии API

В следующей таблице перечислены версии API для ресурсов, используемых в этом шаблоне.

| Ресурс | Тип ресурса | Версия API |
|:---|:---|:---|
| Рабочая область | workspaces | 2017-03-15-preview |
| Учетная запись службы автоматизации | служба автоматизации | 2015-10-31 | 
| Решение | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Перед использованием шаблона

Если вы решите установить и использовать PowerShell локально, эта статья требует модуль Azure PowerShell Az. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если необходимо выполнить обновление, см. статью об [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если вы работаете на уровне PowerShell локально, вам также необходимо запустить [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) для создания соединения с Azure. С Azure PowerShell развертывание использует [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment)

Если вы решили установить и использовать CLI локально, эта статья требует, чтобы вы запускали версию Azure CLI 2.1.0 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). С Azure CLI это развертывание использует [создание группы az.](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) 

Шаблон JSON настроен, чтобы подсказать вам:

* Название рабочей области
* Регион, в котором создается рабочее пространство
* Название учетной записи Автоматизация
* Регион, в котором можно создать учетную запись

Шаблон JSON определяет значение по умолчанию для других параметров, которые могут быть использованы для стандартной конфигурации в вашей среде. Шаблон можно хранить в учетной записи хранения Azure для общего доступа в организации. Для получения дополнительной информации о работе с шаблонами см. [Ресурсы Развертывание с шаблонами «Менеджер ресурсов» и Azure CLI.](../azure-resource-manager/templates/deploy-cli.md)

Следующие параметры в шаблоне устанавливаются со значением по умолчанию для рабочего пространства Log Analytics:

* sku — по умолчанию используется новый тарифный план с платой за гигабайт, выпущенный в апреле 2018 года.
* хранение данных - по умолчанию до тридцати дней
* резервирование емкости - по умолчанию до 100 ГБ

>[!WARNING]
>При создании или настройке рабочего пространства Log Analytics в подписке, которая выбрала модель ценообразования в апреле 2018 года, единственным допустимым уровнем ценообразования Log Analytics является **PerGB2018.**
>

>[!NOTE]
>Прежде чем использовать этот шаблон, просмотрите [дополнительные детали,](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) чтобы полностью понять параметры конфигурации рабочего пространства, такие как режим управления доступом, уровень ценообразования, сохранение и уровень резервирования емкости. Если вы новичок в журналах Azure Monitor и уже не развернули рабочее пространство, следует ознакомиться с руководством по [проектированию рабочего пространства,](../azure-monitor/platform/design-logs-deployment.md) чтобы узнать о контроле доступа, и понять стратегии реализации дизайна, которые мы рекомендуем для вашей организации.

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
        "sku": {
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
                    "Name": "[parameters('sku')]",
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

2. Отредактируйте шаблон с учетом ваших требований. Рассмотрите возможность создания [файла параметров ресурсного менеджера](../azure-resource-manager/templates/parameter-files.md) вместо передачи параметров в качестве входиных значений.

3. Сохранить этот файл в локальной папке как **deployUMSolutiontemplate.json.**

4. Теперь вы можете развернуть этот шаблон. Вы можете использовать либо PowerShell, либо Azure CLI. Когда вам будет предложено учесть рабочее пространство и имя учетной записи Automation, укажите имя, которое является уникальным во всем мире во всех подписках Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

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