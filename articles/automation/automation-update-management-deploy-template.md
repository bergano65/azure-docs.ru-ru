---
title: Включение Управления обновлениями с помощью шаблона Azure Resource Manager | Документация Майкрософт
description: В этой статье описывается, как использовать шаблон Azure Resource Manager для включения Управления обновлениями.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 0a83117d6d58f45d6ee1de2b8d61c2157738fc75
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830997"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Включение Управления обновлениями с помощью шаблона Azure Resource Manager

Для включения Управления обновлениями службы автоматизации Azure в группе ресурсов можно использовать [шаблоны Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). В этой статье приводится пример шаблона, который автоматизирует выполнение следующих задач:

* создание рабочей области Log Analytics в Azure Monitor;
* создание учетной записи службы автоматизации Azure;
* связывание учетной записи службы автоматизации с рабочей областью Log Analytics, если такая связь отсутствует;
* включение Управления обновлениями.

Этот шаблон не автоматизирует включение одной или нескольких виртуальных машин, предоставляемых Azure или сторонними поставщиками.

Если у вас уже есть рабочая область Log Analytics и учетная запись службы автоматизации, развернутая в поддерживаемом регионе в вашей подписке, они не связаны между собой. В рабочей области еще не включено Управление обновлениями. С помощью этого шаблона вы создадите ссылку и развернете Управление обновлениями для виртуальных машин. 

>[!NOTE]
>Пользователь **nxautomation**, включенный в Управление обновлениями в Linux, выполняет только подписанные последовательности runbook.

## <a name="api-versions"></a>Версии API

В следующей таблице перечислены версии API для ресурсов, используемых в этом шаблоне.

| Ресурс | Тип ресурса | Версия API |
|:---|:---|:---|
| Рабочая область | workspaces | 2017-03-15-preview |
| Учетная запись службы автоматизации | служба автоматизации | 2015-10-31 | 
| Решение | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Перед применением шаблона

Чтобы установить и использовать PowerShell локально для выполнения задач из этой статьи, вам понадобится модуль Az Azure PowerShell. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если необходимо выполнить обновление, см. статью об [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0), чтобы создать подключение к Azure. В Azure PowerShell развертывание использует [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.1.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). При работе с Azure CLI это развертывание использует [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Во время работы с шаблоном JSON вам нужно будет ввести следующие сведения:

* имя рабочей области;
* регион для создания рабочей области;
* имя учетной записи службы автоматизации;
* регион для создания этой учетной записи.

Шаблон в формате JSON указывает значения по умолчанию для других параметров, которые в вашей среде скорее всего будут использоваться в стандартной конфигурации. Шаблон можно сохранить в учетной записи хранения Azure для совместного использования в пределах организации. Дополнительную информацию о работе с шаблонами см. в руководстве [Развертывание ресурсов с помощью шаблонов ARM и Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Следующие параметры в шаблоне получают стандартное значение для рабочей области Log Analytics:

* sku — по умолчанию используется новый тарифный план с платой за гигабайт, выпущенный в апреле 2018 года.
* Срок хранения данных — 30 дней по умолчанию.
* Резервирование мощности — 100 ГБ по умолчанию.

>[!WARNING]
>При создании или настройке рабочей области Log Analytics в подписке, использующей модель ценообразования от апреля 2018 года, доступна только ценовая категория **PerGB2018**.
>

Шаблон в формате JSON указывает значения по умолчанию для других параметров, которые, скорее всего, будут использоваться в качестве стандартной конфигурации в вашей среде. Шаблон можно сохранить в учетной записи хранения Azure для совместного использования в пределах организации. Дополнительную информацию о работе с шаблонами см. в руководстве [Развертывание ресурсов с помощью шаблонов ARM и Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Если вы не работали со службой автоматизации Azure и Azure Monitor, вам нужно ознакомиться с указанными ниже сведениями о конфигурации, чтобы избежать ошибок при попытке создать, настроить и использовать рабочую область Log Analytics, связанную с новой учетной записью службы автоматизации.

* Просмотрите [дополнительные сведения](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace), чтобы узнать о вариантах конфигурации рабочей области, таких как режим управления доступом, ценовая категория, период удержания и уровень резервирования мощности.

* Так как для связывания рабочей области с Log Analytics и учетной записью службы автоматизации в подписке поддерживаются только определенные регионы, проверьте [сопоставления рабочих областей](how-to/region-mappings.md), чтобы указать поддерживаемые регионы в строке или файле параметров.

* Если вы не работали с журналами Azure Monitor и еще не развертывали рабочую область, ознакомьтесь с рекомендациями по [проектированию рабочей области](../azure-monitor/platform/design-logs-deployment.md), чтобы узнать о механизмах управления доступом и стратегиях реализации, которые мы рекомендуем использовать в вашей организации.

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

2. Отредактируйте шаблон с учетом ваших требований. Вместо встраивания параметров в строку запуска попробуйте создать [файл параметров Resource Manager](../azure-resource-manager/templates/parameter-files.md).

3. Сохраните этот файл с именем **deployUMSolutiontemplate.json** в локальной папке.

4. Теперь вы можете развернуть этот шаблон. Это можно сделать с помощью PowerShell или Azure CLI. При появлении запроса на ввод имени рабочей области и учетной записи службы автоматизации укажите имя, глобально уникальное во всех подписках Azure.

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

* Дополнительные сведения об использовании Управления обновлениями для виртуальных машин см. в статье [Управление обновлениями и исправлениями для виртуальных машин Azure](automation-tutorial-update-management.md).
* Если рабочая область Log Analytics вам больше не нужна, выполните инструкции из статьи [Отмена связи рабочей области с учетной записью службы автоматизации для Управления обновлениями](automation-unlink-workspace-update-management.md).
* Дополнительные сведения см. в статье [Удаление виртуальной машины из Управления обновлениями](automation-remove-vms-from-update-management.md).
* Дополнительные сведения см. в статье [Устранение неполадок с Управлением обновлениями](troubleshoot/update-management.md).
* Дополнительные сведения см. в статье [Устранение неполадок с агентом обновления Windows](troubleshoot/update-agent-issues.md).
* Дополнительные сведения см. в статье [Устранение неполадок с агентом обновления Linux](troubleshoot/update-agent-issues-linux.md).