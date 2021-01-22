---
title: Включение работоспособности гостевых виртуальных машин в Azure Monitor (предварительная версия)
description: В этой статье описано, как включить Azure Monitor для виртуальных машин работоспособности гостевых систем в подписке и как подключить виртуальные машины.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.custom: references_regions
ms.openlocfilehash: 902bcaa97658802b35fb523a1213e6bbd47f357f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684514"
---
# <a name="enable-azure-monitor-for-vms-guest-health-preview"></a>Включение работоспособности гостевых виртуальных машин в Azure Monitor (предварительная версия)
Azure Monitor для виртуальных машин работоспособности гостевых систем позволяет просматривать работоспособность виртуальной машины в соответствии с набором измерений производительности, которые вычисляются с регулярным интервалом. В этой статье описывается, как включить эту функцию в подписке и как включить гостевой мониторинг для каждой виртуальной машины.

## <a name="current-limitations"></a>Текущие ограничения
Azure Monitor для виртуальных машин работоспособности гостевой системы имеет следующие ограничения в общедоступной предварительной версии:

- В настоящее время поддерживаются только виртуальные машины Azure. В настоящее время служба "Дуга Azure для серверов" не поддерживается.


## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы
Виртуальная машина должна работать под управлением одной из следующих операционных систем: 

  - Ubuntu 16,04 LTS, Ubuntu 18,04 LTS
  - Windows Server 2012 или более поздней версии;

## <a name="supported-regions"></a>Поддерживаемые регионы

Виртуальная машина должна находиться в одном из следующих регионов:

- Центральная Австралия
- Восточная Австралия
- Australia Southeast
- Центральная Индия
- Центральная часть США
- Восточная Азия
- Восточная часть США
- восточная часть США 2
- Восточная часть США 2 (EUAP)
- Центрально-Западная Германия
- Japan East
- Центрально-северная часть США
- Северная Европа
- Центрально-южная часть США
- Юго-Восточная Азия
- южная часть Соединенного Королевства
- центрально-западная часть США
- Западная Европа
- западная часть США
- западная часть США 2


Рабочая область Log Analytics должна находиться в одном из следующих регионов:

- Центральная часть США
- Восточная часть США
- восточная часть США 2
- Восточная часть США 2 (EUAP)
- Северная Европа
- Юго-Восточная Азия
- южная часть Соединенного Королевства
- Западная Европа, регион
- Западная часть США 2

## <a name="prerequisites"></a>Предварительные условия

- Виртуальная машина должна быть подключена к Azure Monitor для виртуальных машин.
- Пользователь, запускающий шаги адаптации, должен иметь минимальный уровень участника доступа к подписке, в которой расположены виртуальная машина и правило сбора данных.
- Требуемые поставщики ресурсов Azure должны быть зарегистрированы, как описано в следующем разделе.

## <a name="register-required-azure-resource-providers"></a>Регистрация необходимых поставщиков ресурсов Azure
Следующие поставщики ресурсов Azure должны быть зарегистрированы для подписки, чтобы включить Azure Monitor для виртуальных машин гостевой работоспособности. 

- Microsoft.WorkloadMonitor
- Microsoft.Insights

Для регистрации поставщика ресурсов можно использовать любой из доступных методов, как описано в разделе [поставщики и типы ресурсов Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Можно также использовать следующий пример команды с помощью armclient, POST или другого метода, чтобы вызвать проверку подлинности Azure Resource Manager:

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Включение виртуальной машины с помощью портал Azure
При включении работоспособности гостя для виртуальной машины в портал Azure выполняется вся необходимая Настройка. Сюда входит создание правила сбора данных, установка расширения работоспособности гостевой системы на виртуальной машине и создание связи с правилом сбора данных.

В представлении **Начало работы** в Azure Monitor для виртуальных машин щелкните ссылку рядом с сообщением об обновлении для виртуальной машины, а затем нажмите кнопку **Обновить** . Можно также выбрать несколько виртуальных машин, чтобы обновить их вместе.

![Включить функцию работоспособности на виртуальной машине](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Включение виртуальной машины с помощью шаблона диспетчер ресурсов
Для включения виртуальных машин с помощью Azure Resource Manager необходимо выполнить три шага.

- Создать правило сбора данных.
- Установите расширение работоспособности гостевой системы на каждой виртуальной машине.
- Создайте связь между виртуальной машиной и правилом сбора данных.

### <a name="create-data-collection-rule-dcr"></a>Создание правила сбора данных (ДКР)

> [!NOTE]
> Если вы включите виртуальную машину с помощью портал Azure, то описанное здесь правило сбора данных будет создано. В этом случае вам не нужно выполнять этот шаг.

Конфигурация мониторов в Azure Monitor для виртуальных машин гостевой работоспособности хранится в [правилах сбора данных (ДКР)](../platform/data-collection-rule-overview.md). Каждая виртуальная машина с расширением работоспособности гостевой системы потребует связи с этим правилом.

> [!NOTE]
> Можно создать дополнительные правила сбора данных, чтобы изменить конфигурацию мониторов по умолчанию, как описано в разделе [Настройка мониторинга в Azure Monitor для виртуальных машин гостевой работоспособности (Предварительная версия)](vminsights-health-configure.md).

Для шаблона требуются значения следующих параметров:

- **дефаулсеалсдатаколлектионруленаме**: сохраняет имя по умолчанию, определенное в шаблоне.
- **дестинатионворкспацересаурцеид**: идентификатор ресурса log Analytics рабочей области, используемой для сбора данных виртуальной машины.
- **датаколлектионрулелокатион**: регион для правила сбора данных. Оно должно соответствовать региону Log Analytics рабочей области.


Разверните шаблон с помощью любого [метода развертывания для шаблонов диспетчер ресурсов](../../azure-resource-manager/templates/deploy-powershell.md). Следующие команды используются для развертывания шаблона и файла параметров с помощью PowerShell или Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---

Правило сбора данных, определенное в приведенном ниже шаблоне диспетчер ресурсов, включает все мониторы для виртуальных машин с расширением работоспособности гостевой системы. Он должен включать источники данных для каждого счетчика производительности, используемого мониторами.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>Пример файла параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "destinationWorkspaceResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



### <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>Установка расширения работоспособности гостевой системы и связывание с правилом сбора данных
Используйте следующий шаблон диспетчер ресурсов, чтобы включить виртуальную машину для работоспособности гостевых систем. Это приведет к установке расширения работоспособности гостевой системы и созданию связи с правилом сбора данных. Этот шаблон можно развернуть с помощью любого [метода развертывания для шаблонов диспетчер ресурсов](../../azure-resource-manager/templates/deploy-powershell.md).


Например, следующие команды позволяют развертывать шаблоны и файлы параметров в группе ресурсов с помощью PowerShell или Azure CLI.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>Файл шаблона

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>Пример файла параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      }
  }
}
```

## <a name="next-steps"></a>Следующие шаги

- [Настройка мониторов, включенных Azure Monitor для виртуальных машин](vminsights-health-configure.md)