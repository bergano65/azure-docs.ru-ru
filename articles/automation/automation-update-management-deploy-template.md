---
title: Использование шаблонов Azure Resource Manager для подключения Управление обновлениями | Документация Майкрософт
description: Вы можете использовать шаблон Azure Resource Manager для подключения решения Управление обновлениями службы автоматизации Azure.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/27/2020
ms.openlocfilehash: a8b382663b56d7481da876979e33194fb0ac533d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925804"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Подключение Управление обновленияминого решения с помощью шаблона Azure Resource Manager

Вы можете использовать [шаблоны Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) , чтобы включить решение Управление обновлениями службы автоматизации Azure в группе ресурсов. В этой статье представлен пример шаблона, который автоматизирует следующие задачи:

* Создание рабочей области Log Analytics Azure Monitor.
* Создание учетной записи службы автоматизации Azure.
* Связывает учетную запись службы автоматизации с Log Analytics рабочей областью, если она еще не связана.
* Подключение решения Управление обновлениями службы автоматизации Azure

Шаблон не автоматизирует подключение одной или нескольких виртуальных машин Azure или других.

Если у вас уже есть рабочая область Log Analytics и учетная запись службы автоматизации, развернутые в поддерживаемом регионе в вашей подписке, они не связаны, а в рабочей области еще не развернуто решение Управление обновлениями, в результате чего будет успешно создан этот шаблон. Ссылка и развертывает решение Управление обновлениями. 

## <a name="api-versions"></a>Версии API

В следующей таблице перечислены версии API для ресурсов, используемых в этом примере.

| Ресурс | Тип ресурса | Версия API |
|:---|:---|:---|
| Рабочая область | рабочие области | 2017-03-15-preview |
| Учетная запись службы автоматизации | автоматизация | 2015-10-31 | 
| Решение | решения | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Перед использованием шаблона

Если вы решили установить и использовать PowerShell локально, для работы с этой статьей требуется модуль Azure PowerShell AZ. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если необходимо выполнить обновление, см. статью об [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure. При использовании Azure PowerShell развертывание использует [New-азресаурцеграупдеплоймент](/powershell/module/az.resources/new-azresourcegroupdeployment).

Если вы решили установить и использовать CLI локально, для работы с этой статьей требуется Azure CLI версии 2.1.0 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). При использовании Azure CLI в этом развертывании используется команду [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Шаблон JSON настроен для запроса:

* Имя рабочей области
* Регион, в котором создается рабочая область
* Имя учетной записи службы автоматизации
* Регион, в котором создается учетная запись

Шаблон JSON определяет значения по умолчанию для других параметров, которые, скорее всего, будут использоваться в качестве стандартной конфигурации в вашей среде. Шаблон можно сохранить в учетной записи хранения Azure для общего доступа в Организации. Дополнительные сведения о работе с шаблонами см. [в статье Развертывание ресурсов с помощью шаблонов диспетчер ресурсов и Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Для следующих параметров в шаблоне задано значение по умолчанию для рабочей области Log Analytics:

* sku — по умолчанию используется новый тарифный план с платой за гигабайт, выпущенный в апреле 2018 года.
* срок хранения данных — по умолчанию — 30 дней.

>[!WARNING]
>При создании или настройке рабочей области Log Analytics в подписке, использующей модель ценообразования от апреля 2018 года, доступна только ценовая категория **PerGB2018**.
>

>[!NOTE]
>Перед использованием этого шаблона ознакомьтесь с [дополнительными сведениями](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) , чтобы полностью понять параметры конфигурации рабочей области, такие как режим управления доступом, ценовая категория, срок хранения и уровень резервирования емкости. Если вы не знакомы с Azure Monitor журналами и еще не развернули рабочую область, ознакомьтесь с руководством по [проектированию рабочей области](../azure-monitor/platform/design-logs-deployment.md) , чтобы узнать об управлении доступом, а также о стратегиях реализации, которые мы рекомендуем использовать для вашей организации.

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

3. Сохраните этот файл как Деплойумсолутионтемплате. JSON в локальной папке.

4. Теперь вы можете развернуть этот шаблон. Можно использовать либо PowerShell, либо Azure CLI. При появлении запроса на ввод имени рабочей области и учетной записи службы автоматизации укажите имя, которое глобально уникально для всех подписок Azure.

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

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы развернули Управление обновлениями решение, вы можете включить виртуальные машины для управления, проверить оценки обновлений и развернуть обновления, чтобы привести их в соответствие.

- Из [учетной записи службы автоматизации Azure](automation-onboard-solutions-from-automation-account.md) для одной или нескольких компьютеров Azure и вручную для компьютеров, не относящихся к Azure.

- Для одной виртуальной машины Azure со страницы виртуальной машины в портал Azure. Этот сценарий доступен для виртуальных машин [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) и [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) .

- Для [нескольких виртуальных машин Azure](manage-update-multi.md) , выбрав их на странице **виртуальные машины** в портал Azure. 