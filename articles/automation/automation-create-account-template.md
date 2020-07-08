---
title: Создание учетной записи службы автоматизации с помощью шаблона Azure Resource Manager | Документация Майкрософт
description: В этой статье содержатся сведения об использовании шаблона Azure Resource Manager для создания учетной записи службы автоматизации Azure.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/09/2020
ms.openlocfilehash: 6b26db522db246add48941da9af4784ed2942a0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84661034"
---
# <a name="create-an-automation-account-using-an-azure-resource-manager-template"></a>Создание учетной записи службы автоматизации с помощью шаблона Azure Resource Manager

Для создания учетной записи службы автоматизации Azure в группе ресурсов можно использовать [шаблоны Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). В этой статье приводится пример шаблона, который:

* автоматизирует создание рабочей области Log Analytics Azure Monitor;
* автоматизирует создание учетной записи службы автоматизации Azure;
* связывает учетную запись службы автоматизации с рабочей областью Log Analytics.

Этот шаблон не позволяет автоматизировать включение виртуальных машин Azure или виртуальных машин, не относящихся к Azure. 

>[!NOTE]
>Создание учетной записи запуска от имени службы автоматизации на основе шаблона Azure Resource Manager не поддерживается. Сведения о создании учетной записи запуска от имени вручную на портале или с помощью PowerShell см. в статье [Управление учетными записями запуска от имени](manage-runas-account.md).

После выполнения этих действий необходимо [настроить параметры диагностики](automation-manage-send-joblogs-log-analytics.md), чтобы учетная запись службы автоматизации могла отправлять состояния заданий Runbook и потоки заданий в связанную рабочую область Log Analytics. 

## <a name="api-versions"></a>Версии API

В следующей таблице перечислены версии API для ресурсов, используемых в этом примере.

| Ресурс | Тип ресурса | Версия API |
|:---|:---|:---|
| Рабочая область | workspaces | 2020-03-01 — предварительная версия |
| Учетная запись службы автоматизации | служба автоматизации | 2018-06-30 | 

## <a name="before-you-use-the-template"></a>Перед использованием шаблона

Чтобы установить и использовать PowerShell локально, для работы с этой статьей вам понадобится модуль Azure PowerShell Az. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если необходимо выполнить обновление, см. статью об [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). При использовании PowerShell на локальном компьютере также нужно запустить `Connect-AzAccount`, чтобы создать подключение к Azure. В PowerShell развертывание использует [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Если вы решили установить и использовать Azure CLI локально, для работы с этой статьей вам понадобится версия 2.1.0 или более поздняя. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). При работе с Azure CLI это развертывание использует [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Шаблон JSON настроен так, чтобы осталось только указать следующие сведения:

* имя рабочей области;
* регион, в котором будет создана рабочая область;
* , Чтобы включить разрешения для ресурсов или рабочих областей.
* Имя учетной записи службы автоматизации.
* Регион для создания учетной записи службы автоматизации в.

Для следующих параметров в шаблоне задано значение по умолчанию для рабочей области Log Analytics:

* *sku* — по умолчанию используется ценовая категория с платой за гигабайт, выпущенная в апреле 2018 года.
* *dataRetention* — по умолчанию задано 30 дней.

>[!WARNING]
>Если вы хотите создать или настроить рабочую область Log Analytics в подписке, использующей модель ценообразования от апреля 2018 года, будет доступна только ценовая категория *PerGB2018*.
>

Шаблон в формате JSON указывает значения по умолчанию для других параметров, которые, скорее всего, будут использоваться в качестве стандартной конфигурации в вашей среде. Шаблон можно хранить в учетной записи хранения Azure для общего доступа в организации. Дополнительную информацию о работе с шаблонами см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Если вы не знакомы со службами автоматизации Azure и Azure Monitor, важно понимать следующие моменты, относящиеся к конфигурации. Они могут помочь избежать ошибок при создании, настройке и использовании рабочей области Log Analytics, связанной с новой учетной записью службы автоматизации.

* Ознакомьтесь с [дополнительными сведениями](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace), чтобы полностью понять возможности конфигурации рабочей области, такие как режим управления доступом, ценовая категория, срок хранения и уровень резервирования мощности.

* Изучите [сопоставления рабочей области](how-to/region-mappings.md), чтобы указывать поддерживаемые регионы в строке или файле параметров. Только определенные регионы поддерживают связывание рабочей области Log Analytics и учетной записи службы автоматизации в подписке.

* Если вы еще не работали с журналами Azure Monitor и пока не развернули рабочую область, ознакомьтесь с [руководством по проектированию рабочей области](../azure-monitor/platform/design-logs-deployment.md). В нем содержатся сведения об управлении доступом и приводятся стратегии реализации разработок, которые рекомендуется внедрить в организации.

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
                "description": "Number of days to retain data."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "resourcePermissions": {
              "type": "bool",
              "metadata": {
                "description": "true to use resource or workspace permissions. false to require workspace permissions."
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
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]",
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
            "apiVersion": "2018-06-30",
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
                        "apiVersion": "2018-06-30",
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
                        "apiVersion": "2018-06-30",
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
                        "apiVersion": "2018-06-30",
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
            "apiVersion": "2020-03-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[parameters('location')]",
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

2. Отредактируйте шаблон с учетом ваших требований. Вместо передачи параметров в виде встроенных значений попробуйте создать [файл параметров Resource Manager](../azure-resource-manager/templates/parameter-files.md).

3. Сохраните этот файл как deployAzAutomationAccttemplate.json в локальной папке.

4. Теперь вы можете развернуть этот шаблон. Это можно сделать с помощью PowerShell или Azure CLI. При появлении запроса на ввод имени рабочей области и учетной записи службы автоматизации укажите имя, глобально уникальное во всех подписках Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    Развертывание может занять несколько минут. По завершении появится сообщение с результатами, похожими на приведенные ниже.

    ![Пример результатов по завершении развертывания](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Дальнейшие действия

Сведения об отправке данных о состоянии заданий и потоков заданий Runbook в связанную рабочую область Log Analytics см. в статье [Пересылка данных задания службы автоматизации Azure в журналы Azure Monitor](automation-manage-send-joblogs-log-analytics.md). С их помощью вы сможете настроить параметры диагностики учетной записи службы автоматизации с помощью команд Azure PowerShell и завершить интеграцию для отправки журналов в рабочую область в целях анализа. 
