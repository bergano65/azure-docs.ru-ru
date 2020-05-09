---
title: Использование шаблонов Azure Resource Manager для создания учетной записи службы автоматизации | Документация Майкрософт
description: Для создания учетной записи службы автоматизации Azure можно использовать шаблон Azure Resource Manager.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 19aee9d5fdf3f4a3d74484bb7cb2e609bc2807b4
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927876"
---
# <a name="create-an-automation-account-by-using-an-azure-resource-manager-template"></a>Создание учетной записи службы автоматизации с помощью шаблона Azure Resource Manager

Для создания учетной записи службы автоматизации Azure в группе ресурсов можно использовать [шаблоны Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) . В этой статье представлен пример шаблона, который:

* Автоматизирует создание Azure Monitor Log Analytics рабочей области.
* Автоматизирует создание учетной записи службы автоматизации Azure.
* Связывает учетную запись службы автоматизации с Log Analytics рабочей областью.

Этот шаблон не автоматизирует подключение виртуальных машин или решений Azure, не относящихся к Azure. 

>[!NOTE]
>Создание учетной записи запуска от имени службы автоматизации не поддерживается, если вы используете шаблон Azure Resource Manager. Сведения о создании учетной записи запуска от имени вручную с портала или с помощью PowerShell см. в разделе [Управление учетными записями запуска от имени](manage-runas-account.md).

## <a name="api-versions"></a>Версии API

В следующей таблице перечислены версии API для ресурсов, используемых в этом примере.

| Ресурс | Тип ресурса | Версия API |
|:---|:---|:---|
| Рабочая область | workspaces | 2017-03-15-preview |
| Учетная запись службы автоматизации | служба автоматизации | 2015-10-31 | 

## <a name="before-you-use-the-template"></a>Перед использованием шаблона

Если вы решили установить и использовать PowerShell локально, для работы с этой статьей требуется модуль Azure PowerShell AZ. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если необходимо выполнить обновление, см. статью об [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). При использовании PowerShell на локальном компьютере также нужно запустить `Connect-AzAccount`, чтобы создать подключение к Azure. При использовании PowerShell в развертывании используется [New-азресаурцеграупдеплоймент](/powershell/module/az.resources/new-azresourcegroupdeployment).

Если вы решили установить и использовать Azure CLI локально, для работы с этой статьей требуется версия 2.1.0 или более поздняя. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). При использовании Azure CLI в этом развертывании используется команду [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Шаблон JSON настроен для запроса:

* Имя рабочей области.
* Регион, в котором создается рабочая область.
* Имя учетной записи службы автоматизации.
* Регион, в котором создается учетная запись.

Для следующих параметров в шаблоне задано значение по умолчанию для рабочей области Log Analytics:

* *номер SKU* по умолчанию равен ценовой категории за ГБ, которая выпущена в модели ценообразования за Апрель 2018.
* значение *retention* по умолчанию — 30 дней.
* *капаЦитиресерватионлевел* по умолчанию — 100 ГБ.

>[!WARNING]
>Если вы хотите создать или настроить Log Analytics рабочую область в подписке, которая выбрала модель ценообразования за Апрель 2018, то единственной допустимой ценовой категорией Log Analytics является *PerGB2018*.
>

Шаблон JSON определяет значения по умолчанию для других параметров, которые, скорее всего, будут использоваться в качестве стандартной конфигурации в вашей среде. Шаблон можно сохранить в учетной записи хранения Azure для общего доступа в Организации. Дополнительные сведения о работе с шаблонами см. [в разделе Развертывание ресурсов с помощью шаблонов диспетчер ресурсов и Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Если вы не знакомы со службой автоматизации Azure и Azure Monitor, важно понимать следующие сведения о конфигурации. Они могут помочь избежать ошибок при попытке создать, настроить и использовать Log Analytics рабочую область, связанную с новой учетной записью службы автоматизации. 

* Ознакомьтесь с [дополнительными сведениями](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) , чтобы полностью понять параметры конфигурации рабочей области, такие как режим управления доступом, ценовая категория, срок хранения и уровень резервирования емкости.

* Проверьте [сопоставления рабочей области](how-to/region-mappings.md) , чтобы указать поддерживаемые регионы в строке или в файле параметров. Для связывания рабочей области Log Analytics и учетной записи службы автоматизации в подписке поддерживаются только определенные регионы.

* Если вы не знакомы с Azure Monitor журналами и еще не развернули рабочую область, ознакомьтесь с [руководством по проектированию рабочей области](../azure-monitor/platform/design-logs-deployment.md). Он поможет вам узнать об управлении доступом и ознакомиться с стратегиями реализации разработки, которые мы рекомендуем использовать для вашей организации.

## <a name="deploy-the-template"></a>Развертывание шаблона

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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can have only 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This applies only when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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

2. Отредактируйте шаблон с учетом ваших требований. Вместо передачи параметров в виде встроенных значений рекомендуется создать [Диспетчер ресурсов файл параметров](../azure-resource-manager/templates/parameter-files.md) .

3. Сохраните этот файл как Деплойазаутоматионаккттемплате. JSON в локальной папке.

4. Теперь вы можете развернуть этот шаблон. Можно использовать либо PowerShell, либо Azure CLI. При появлении запроса на ввод имени рабочей области и учетной записи службы автоматизации укажите имя, которое глобально уникально для всех подписок Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    Для завершения развертывания может потребоваться несколько минут. В этом случае вы увидите примерно следующее сообщение, содержащее результат.

    ![Пример результатов по завершении развертывания](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда у вас есть учетная запись службы автоматизации, вы можете создавать модули Runbook и автоматизировать ручные процессы.

* Сведения о том, как начать работу с runbook, см. в статье о [создании runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, см. статью [Создание Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md).
* Чтобы приступить к работе с модулями Runbook Python 2, см. статью [Создание Runbook Python](automation-first-runbook-textual-python2.md).