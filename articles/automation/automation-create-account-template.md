---
title: Используйте шаблоны менеджера ресурсов Azure для создания учетной записи автоматизации Документы Майкрософт
description: Для создания учетной записи Azure Automation можно использовать шаблон менеджера ресурсов Azure.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2020
ms.openlocfilehash: efe51fbada8ac70b24c16a5c7c1e0e91879e5e9f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618689"
---
# <a name="create-automation-account-using-azure-resource-manager-template"></a>Создание учетной записи автоматизации с помощью шаблона управления ресурсами Azure

Шаблоны [менеджеров ресурсов Azure](../azure-resource-manager/templates/template-syntax.md) можно использовать для создания учетной записи Azure Automation в группе ресурсов. В этой статье приводится пример шаблона, который автоматизирует следующее:

* Создание рабочего пространства анализа журналов Azure Monitor Analytics.
* Создание учетной записи Azure Automation.
* Ссылки на учетную запись Автоматизации с рабочим пространством Log Analytics.

Шаблон не автоматизировать посадку одного или нескольких VMs Azure или не-Azure VMs или решений. 

>[!NOTE]
>Создание учетной записи Automation Run As не поддерживается при использовании шаблона управления ресурсами Azure. Чтобы создать учетную запись Run As вручную с портала или с Помощью PowerShell, [см.](manage-runas-account.md)

## <a name="api-versions"></a>Версии API

В следующей таблице перечислены версии API для ресурсов, используемых в этом примере.

| Ресурс | Тип ресурса | Версия API |
|:---|:---|:---|
| Рабочая область | workspaces | 2017-03-15-preview |
| Учетная запись службы автоматизации | служба автоматизации | 2015-10-31 | 

## <a name="before-using-the-template"></a>Перед использованием шаблона

Если вы решите установить и использовать PowerShell локально, эта статья требует модуль Azure PowerShell Az. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если необходимо выполнить обновление, см. статью об [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure. С Azure PowerShell развертывание использует [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment)

Если вы решили установить и использовать CLI локально, эта статья требует, чтобы вы запускали версию Azure CLI 2.1.0 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). С Azure CLI это развертывание использует [создание группы az.](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) 

Шаблон JSON настроен, чтобы подсказать вам:

* Название рабочей области
* Регион для создания рабочего пространства в
* Название учетной записи Автоматизация
* Регион для создания учетной записи в

Шаблон JSON определяет значение по умолчанию для других параметров, которые, вероятно, будут использоваться в качестве стандартной конфигурации в вашей среде. Шаблон можно хранить в учетной записи хранения Azure для общего доступа в организации. Для получения дополнительной информации о работе с шаблонами см. [Ресурсы Развертывание с шаблонами «Менеджер ресурсов» и Azure CLI.](../azure-resource-manager/templates/deploy-cli.md)

Следующие параметры в шаблоне устанавливаются со значением по умолчанию для рабочего пространства Log Analytics:

* sku — по умолчанию используется новый тарифный план с платой за гигабайт, выпущенный в апреле 2018 года.
* хранение данных - по умолчанию до тридцати дней
* резервирование емкости - по умолчанию до 100 ГБ

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
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
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
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
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
     ]
    }
    ```

2. Отредактируйте шаблон с учетом ваших требований. Рассмотрите возможность создания [файла параметров ресурсного менеджера](../azure-resource-manager/templates/parameter-files.md) вместо передачи параметров в качестве входиных значений.

3. Сохраните этот файл при развертыванииAzAutomationAccttemplate.json в локальной папке.

4. Теперь вы можете развернуть этот шаблон. Вы можете использовать либо PowerShell, либо Azure CLI. Когда вам будет предложено учесть рабочее пространство и имя учетной записи Automation, укажите имя, которое является уникальным во всем мире во всех подписках Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    Развертывание может занять несколько минут. По завершении выполнения появится сообщение с результатами наподобие приведенного ниже.

    ![Пример результатов по завершении развертывания](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда у вас есть учетная запись Автоматизация, вы можете создавать runbooks и автоматизировать ручные процессы.
