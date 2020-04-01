---
title: Создание оповещения метрики с помощью шаблона Resource Manager
description: Сведения об использовании шаблона Resource Manager для создания оповещения метрики.
author: harelbr
ms.author: harelbr
services: azure-monitor
ms.topic: conceptual
ms.date: 2/24/2020
ms.subservice: alerts
ms.openlocfilehash: fe1f1d2700619bfcac4512bb0e631262edf8401c
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398099"
---
# <a name="create-a-metric-alert-with-a-resource-manager-template"></a>Создание оповещения метрики с помощью шаблона Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

В этой статье показано, как можно использовать [шаблон Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) для настройки [новых оповещений метрик](../../azure-monitor/platform/alerts-metric-near-real-time.md) в Azure Monitor. Шаблоны Resource Manager позволяют программно настроить оповещения согласованным и воспроизводимым образом в разных средах. Сейчас в [этом наборе типов ресурсов](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) доступны новые оповещения метрик.

> [!IMPORTANT]
> Шаблон ресурсов для создания метрических оповещений для типа ресурса: Рабочее пространство analytics Azure Log (т.е.) `Microsoft.OperationalInsights/workspaces`требует дополнительных шагов. См. дополнительные сведения о [шаблоне ресурсов для создания оповещения метрик для журналов](../../azure-monitor/platform/alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

Основными шагами являются следующие:

1. Используйте один из приведенных ниже шаблонов в виде JSON-файла, описывающего создание оповещения.
2. Отспособите и используйте файл соответствующих параметров в качестве JSON для настройки оповещения.
3. Для `metricName` параметра см. доступные метрики в [поддерживаемых Azure Monitor метриках.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)
4. Разверните шаблон с помощью [любого метода развертывания](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="template-for-a-simple-static-threshold-metric-alert"></a>Шаблон для простого оповещения о метрике со статическим пороговым значением

Чтобы создать оповещение с помощью шаблона Resource Manager, создайте ресурс типа `Microsoft.Insights/metricAlerts` и заполните все связанные свойства. Ниже приведен пример шаблона, создающий правило генерации оповещений для метрики.

В целях данного пошагового руководства сохраните приведенный ниже JSON в виде файла simplestaticmetricalert.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Описание схемы и свойств правила генерации оповещений [доступно здесь](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate).

Вы можете задать значения для параметров с помощью командной строки или файла параметров. Ниже приведен пример файла параметров.

Сохраните приведенный ниже JSON в виде файла simplestaticmetricalert.parameters.json и измените его соответствующим образом.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Metric Alert"
        },
        "alertDescription": {
            "value": "New metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/Microsoft.Compute/virtualMachines/replace-with-resource-name"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "80"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```


Вы можете создать оповещение метрики с помощью шаблона и файла параметров, используя PowerShell или Azure CLI.

Использование Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile simplestaticmetricalert.json -TemplateParameterFile simplestaticmetricalert.parameters.json
```


Использование Azure CLI
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file simplestaticmetricalert.json \
    --parameters @simplestaticmetricalert.parameters.json
```

> [!NOTE]
>
> Хотя оповещение метрики можно создать в группе ресурсов, отличной от целевого ресурса, рекомендуется использовать ту же группу ресурсов, что и целевой ресурс.

## <a name="template-for-a-simple-dynamic-thresholds-metric-alert"></a>Шаблон для простого оповещения о метрике с динамическим пороговым значением

Чтобы создать оповещение с помощью шаблона Resource Manager, создайте ресурс типа `Microsoft.Insights/metricAlerts` и заполните все связанные свойства. Ниже приведен пример шаблона, создающий правило генерации оповещений для метрики.

В целях данного пошагового руководства сохраните приведенный ниже JSON в виде файла simpledynamicmetricalert.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
    "ignoreDataBefore": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Use this option to set the date from which to start learning the metric historical data and calculate the dynamic thresholds (in ISO8601 format, e.g. '2019-12-31T22:00:00Z')."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                "ignoreDataBefore": "[parameters('ignoreDataBefore')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Описание схемы и свойств правила генерации оповещений [доступно здесь](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate).

Вы можете задать значения для параметров с помощью командной строки или файла параметров. Ниже приведен пример файла параметров. 

Сохраните приведенный ниже JSON в виде файла simpledynamicmetricalert.parameters.json и измените его соответствующим образом.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Metric Alert with Dynamic Thresholds"
        },
        "alertDescription": {
            "value": "New metric alert with Dynamic Thresholds created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/Microsoft.Compute/virtualMachines/replace-with-resource-name"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
    "ignoreDataBefore": {
            "value": ""
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```


Вы можете создать оповещение метрики с помощью шаблона и файла параметров, используя PowerShell или Azure CLI.

Использование Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile simpledynamicmetricalert.json -TemplateParameterFile simpledynamicmetricalert.parameters.json
```


Использование Azure CLI
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file simpledynamicmetricalert.json \
    --parameters @simpledynamicmetricalert.parameters.json
```

> [!NOTE]
>
> Хотя оповещение метрики можно создать в группе ресурсов, отличной от целевого ресурса, рекомендуется использовать ту же группу ресурсов, что и целевой ресурс.

## <a name="template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria"></a>Шаблон для метрика статический порог, который отслеживает несколько критериев

Новые оповещения метрик поддерживают оповещения о многомерных метриках, а также несколько критериев. Вы можете использовать следующий шаблон для создания более продвинутого правила предупреждения метрики о мерных метриках и указания нескольких критериев.

Пожалуйста, обратите внимание на следующие ограничения при использовании измерений в правиле оповещения, содержащем несколько критериев:
- Вы можете выбрать только одно значение на измерение в пределах каждого критерия.
- Вы не\*можете использовать " " как значение измерения.
- Когда метрики, настроенные в разных критериях, поддерживают одно и то же измерение, то для всех этих показателей (в соответствующих критериях) необходимо четко установить точно ежее значение накидке измерения.
    - В приведенном ниже примере, поскольку метрики **транзакций** и **SuccessE2ELatency** имеют измерение **ApiName,** а *критерий1* определяет значение *"GetBlob"* для измерения **ApiName,** затем *criteron2* также должен установить значение *"GetBlob"* для измерения **ApiName.**


В целях данного пошагового руководства сохраните приведенный ниже JSON в виде файла advancedstaticmetricalert.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion1":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "criterion2": {
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criterion1": "[array(parameters('criterion1'))]",
        "criterion2": "[array(parameters('criterion2'))]",
        "criteria": "[concat(variables('criterion1'),variables('criterion2'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Приведенный выше шаблон можно использовать вместе с файлом параметров, указанным ниже. 

В целях данного пошагового руководства сохраните и измените приведенный ниже JSON в виде файла advancedstaticmetricalert.parameters.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Multi-dimensional Metric Alert (Replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template (Replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion1": {
            "value": {
                    "name": "1st criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Include",
                            "values": ["Success"]
                        },
                        {
                            "name":"ApiName",
                            "operator": "Include",
                            "values": ["GetBlob"]
                        }
                    ],
                    "operator": "GreaterThan",
                    "threshold": "5",
                    "timeAggregation": "Total"
                }
        },
        "criterion2": {
            "value":{
                "name": "2nd criterion",
                "metricName": "SuccessE2ELatency",
                "dimensions": [
                    {
                        "name":"ApiName",
                        "operator": "Include",
                        "values": ["GetBlob"]
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "250",
                "timeAggregation": "Average"
            }
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }
}
```


Вы можете создать оповещение метрики с помощью шаблона и файла параметров, используя PowerShell или Azure CLI из текущего рабочего каталога.

Использование Azure PowerShell
```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile advancedstaticmetricalert.json -TemplateParameterFile advancedstaticmetricalert.parameters.json
```



Использование Azure CLI
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file advancedstaticmetricalert.json \
    --parameters @advancedstaticmetricalert.parameters.json
```


## <a name="template-for-a-static-metric-alert-that-monitors-multiple-dimensions"></a>Шаблон для статического метеосигнала, который отслеживает несколько измерений

Вы можете использовать следующий шаблон для создания статического правила предупреждения метрик и мерных метрик.

Правило единого оповещения может отслеживать несколько метрических временных рядов одновременно, что приводит к меньшему количеству правил оповещения для управления.

В приведенном ниже примере правило оповещения отслеживает значения размеров измерений измерений **ResponseType** и **ApiName** для метрики **транзакций:**
1. **ResponsType** - Использование подстановочного знака означает,\*что для каждого значения измерения **ResponseType,** включая будущие значения, различные временные ряды отслеживаются индивидуально.
2. **ApiName** - Различные временные ряды отслеживаются только для значений измерения **GetBlob** и **PutBlob.**

Например, некоторые из потенциальных временных рядов, которые контролируются этим правилом оповещения:
- Метрика - *Транзакции*, ResponseType - *Успех*, ApiName - *GetBlob*
- Метрика - *Транзакции*, ResponseType *PutBlob* - *Успех*, ApiName
- Метрика - *Транзакции*, ResponseType - *Тайм-аут сервера,* ApiName - *GetBlob*
- Метрика - *Транзакции*, ResponseType - *Тайм-аут сервера,* ApiName - *PutBlob*

Сохранить Json ниже, как multidimensionalstaticmetricmetricalert.json для целей этого пошагового покидения.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criteria": "[array(parameters('criterion'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Приведенный выше шаблон можно использовать вместе с файлом параметров, указанным ниже. 

Сохранить и изменить json ниже, как multidimensionalstaticmetricmetricalert.parameters.json для целей этого пошагового покисты.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New multi-dimensional metric alert rule (replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert rule created via template (replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion": {
            "value": {
                    "name": "Criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Include",
                            "values": ["*"]
                        },
                        {
                "name":"ApiName",
                            "operator": "Include",
                            "values": ["GetBlob", "PutBlob"]    
                        }
                    ],
                    "operator": "GreaterThan",
                    "threshold": "5",
                    "timeAggregation": "Total"
                }
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }
}
```


Вы можете создать оповещение метрики с помощью шаблона и файла параметров, используя PowerShell или Azure CLI из текущего рабочего каталога.

Использование Azure PowerShell
```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile multidimensionalstaticmetricalert.json -TemplateParameterFile multidimensionalstaticmetricalert.parameters.json
```



Использование Azure CLI
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file multidimensionalstaticmetricalert.json \
    --parameters @multidimensionalstaticmetricalert.parameters.json
```


## <a name="template-for-a-dynamic-thresholds-metric-alert-that-monitors-multiple-dimensions"></a>Шаблон для метеосигнала динамических порогов, который отслеживает несколько измерений

Вы можете использовать следующий шаблон для создания более продвинутого правила метрикарной оповещения динамических порогов на мерных метриках.

Правило предупреждения динамических порогов может создавать индивидуальные пороги для сотен метрических временных рядов (даже различных типов) одновременно, что приводит к меньшему количеству правил оповещения для управления.

В приведенном ниже примере правило оповещения отслеживает значения размеров измерений измерений **ResponseType** и **ApiName** для метрики **транзакций:**
1. **ResponsType** - Для каждого значения измерения **ResponseType,** включая будущие значения, различные временные ряды контролируются индивидуально.
2. **ApiName** - Различные временные ряды отслеживаются только для значений измерения **GetBlob** и **PutBlob.**

Например, некоторые из потенциальных временных рядов, которые контролируются этим правилом оповещения:
- Метрика - *Транзакции*, ResponseType - *Успех*, ApiName - *GetBlob*
- Метрика - *Транзакции*, ResponseType *PutBlob* - *Успех*, ApiName
- Метрика - *Транзакции*, ResponseType - *Тайм-аут сервера,* ApiName - *GetBlob*
- Метрика - *Транзакции*, ResponseType - *Тайм-аут сервера,* ApiName - *PutBlob*

В целях данного пошагового руководства сохраните приведенный ниже JSON в виде файла advanceddynamicmetricalert.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criteria": "[array(parameters('criterion'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Приведенный выше шаблон можно использовать вместе с файлом параметров, указанным ниже. 

В целях данного пошагового руководства сохраните и измените приведенный ниже JSON в виде файла advanceddynamicmetricalert.parameters.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Multi-dimensional Metric Alert with Dynamic Thresholds (Replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert with Dynamic Thresholds created via template (Replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion": {
            "value": {
                    "criterionType": "DynamicThresholdCriterion",
                    "name": "1st criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Include",
                            "values": ["*"]
                        },
                        {
                            "name":"ApiName",
                            "operator": "Include",
                            "values": ["GetBlob", "PutBlob"]
                        }
                    ],
                    "operator": "GreaterOrLessThan",
                    "alertSensitivity": "Medium",
                    "failingPeriods": {
                        "numberOfEvaluationPeriods": "4",
                        "minFailingPeriodsToAlert": "3"
                    },
                    "timeAggregation": "Total"
                }
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }
}
```


Вы можете создать оповещение метрики с помощью шаблона и файла параметров, используя PowerShell или Azure CLI из текущего рабочего каталога.

Использование Azure PowerShell
```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile advanceddynamicmetricalert.json -TemplateParameterFile advanceddynamicmetricalert.parameters.json
```



Использование Azure CLI
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file advanceddynamicmetricalert.json \
    --parameters @advanceddynamicmetricalert.parameters.json
```

>[!NOTE]
>
> Несколько критериев в настоящее время не поддерживаются для правил метеосигнала оповещения, которые используют динамические пороги.


## <a name="template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric"></a>Шаблон для метрика статического порога, который отслеживает пользовательскую метрику

Вы можете использовать следующий шаблон для создания более продвинутого правила статической метрики порога на пользовательской метрике.

Чтобы узнать больше об пользовательских метриках в Azure Monitor, [см.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-custom-overview)

При создании правила оповещения на пользовательской метрике необходимо указать как имя метрики, так и пространство имен метрики. Вы также должны убедиться, что пользовательский метрики уже сообщается, так как вы не можете создать правило оповещения на пользовательской метрике, которая еще не существует.

Сохранить Json ниже, как customstaticmetricalert.json для целей этого пошагового покидения.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "metricNamespace": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Namespace of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "How often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "metricNamespace": "[parameters('metricNamespace')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Приведенный выше шаблон можно использовать вместе с файлом параметров, указанным ниже. 

Сохранить и изменить json ниже, как customstaticmetricalert.parameters.json для целей этого пошагового листа.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New alert rule on a custom metric"
        },
        "alertDescription": {
            "value": "New alert rule on a custom metric created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/microsoft.insights/components/replace-with-application-insights-resource-name"
        },
        "metricName": {
            "value": "The custom metric name"
        },
        "metricNamespace": {
            "value": "Azure.ApplicationInsights"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "80"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```


Вы можете создать оповещение метрики с помощью шаблона и файла параметров, используя PowerShell или Azure CLI из текущего рабочего каталога.

Использование Azure PowerShell
```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupOfTargetResource `
  -TemplateFile customstaticmetricalert.json -TemplateParameterFile customstaticmetricalert.parameters.json
```



Использование Azure CLI
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupOfTargetResource \
    --template-file customstaticmetricalert.json \
    --parameters @customstaticmetricalert.parameters.json
```

>[!NOTE]
>
> Вы можете найти метрику пространства имен определенной пользовательской [метрики, просматривая пользовательские метрики через портал Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-custom-overview#browse-your-custom-metrics-via-the-azure-portal)


## <a name="template-for-a-metric-alert-that-monitors-multiple-resources"></a>Шаблон для метеопредупреждения, который отслеживает несколько ресурсов

В предыдущих разделах описаны примеры шаблонов Azure Resource Manager для создания оповещений на основе метрик, которые отслеживают один ресурс. Теперь Azure Monitor поддерживает мониторинг нескольких ресурсов (одного и того же типа) с помощью единого правила предупреждения метрики для ресурсов, которые существуют в том же регионе Azure. В настоящее время эта функция поддерживается только в общедоступном облаке Azure и только для виртуальных машин, баз данных серверов S'L, эластичных пулов серверов S'L и устройств с краями Databox. Кроме того, эта функция доступна только для метрик платформы и не поддерживается для пользовательских метрик.

Правило генерации оповещений с динамическим пороговым значением также может помочь в создании точно настроенных порогов для нескольких сотен метрик (различных типов) одновременно, что приведет к управлению небольшим числом правил генерации оповещений.

В этом разделе описываются шаблоны Azure Resource Manager для трех сценариев мониторинга нескольких ресурсов с помощью одного правила.

- Мониторинг всех виртуальных машин (в одном регионе Azure) в одной или нескольких группах ресурсов.
- Мониторинг всех виртуальных машин (в одном регионе Azure) в подписке.
- Мониторинг списка виртуальных машин (в одном регионе Azure) в подписке.

> [!NOTE]
>
> В правиле предупреждения метрики, которое отслеживает несколько ресурсов, допускается только одно условие.

### <a name="static-threshold-alert-on-all-virtual-machines-in-one-or-more-resource-groups"></a>Оповещение со статическим пороговым значением на всех виртуальных машинах в одной или нескольких группах ресурсов

Этот шаблон позволяет создать правило генерации оповещений о метрике со статическим пороговым значением, которое отслеживает процент загрузки ЦП для всех виртуальных машин (в одном регионе Azure) в одной или нескольких группах ресурсов.

Для работы с этим пошаговым руководством сохраните приведенный ниже JSON в виде файла all-vms-in-resource-group-static.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetResourceGroup":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "Full path of the resource group(s) where target resources to be monitored are in. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": "[parameters('targetResourceGroup')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Приведенный выше шаблон можно использовать с файлом параметров, указанным ниже.
Для работы с этим пошаговым руководством сохраните и измените приведенный ниже JSON в виде файла all-vms-in-resource-group-static.parameters.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource metric alert via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceGroup":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "0"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

Вы можете создать статическое оповещение о метрике с помощью шаблона и файла параметров, используя PowerShell или Azure CLI из текущей рабочей папки.

Использование Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile all-vms-in-resource-group-static.json -TemplateParameterFile all-vms-in-resource-group-static.parameters.json
```

Использование Azure CLI

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file all-vms-in-resource-group-static.json \
    --parameters @all-vms-in-resource-group-static.parameters.json
```

### <a name="dynamic-thresholds-alert-on-all-virtual-machines-in-one-or-more-resource-groups"></a>Оповещение с динамическим пороговым значением на всех виртуальных машинах в одной или нескольких группах ресурсов

Этот шаблон позволяет создать правило генерации оповещений о метрике с динамическим пороговым значением, которое отслеживает процент загрузки ЦП для всех виртуальных машин (в одном регионе Azure) в одной или нескольких группах ресурсов.

Для работы с этим пошаговым руководством сохраните приведенный ниже JSON в виде файла all-vms-in-resource-group-dynamic.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetResourceGroup":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "Full path of the resource group(s) where target resources to be monitored are in. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": "[parameters('targetResourceGroup')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Приведенный выше шаблон можно использовать с файлом параметров, указанным ниже.
Для работы с этим пошаговым руководством сохраните и измените приведенный ниже JSON в виде файла all-vms-in-resource-group-dynamic.parameters.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource metric alert with Dynamic Thresholds via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert with Dynamic Thresholds created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceGroup":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

Вы можете создать оповещение метрики с помощью шаблона и файла параметров, используя PowerShell или Azure CLI из текущего рабочего каталога.

Использование Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile all-vms-in-resource-group-dynamic.json -TemplateParameterFile all-vms-in-resource-group-dynamic.parameters.json
```

Использование Azure CLI

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file all-vms-in-resource-group-dynamic.json \
    --parameters @all-vms-in-resource-group-dynamic.parameters.json
```

### <a name="static-threshold-alert-on-all-virtual-machines-in-a-subscription"></a>Оповещение со статическим пороговым значением на всех виртуальных машинах в подписке

Этот шаблон позволяет создать правило генерации оповещений о метрике со статическим пороговым значением, которое отслеживает процент загрузки ЦП для всех виртуальных машин (в одном регионе Azure) в подписке.

Для работы с этим пошаговым руководством сохраните приведенный ниже JSON в виде файла all-vms-in-subscription-static.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetSubscription":{
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Azure Resource Manager path up to subscription ID. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('targetSubscription')]"],
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Приведенный выше шаблон можно использовать с файлом параметров, указанным ниже.
Для работы с этим пошаговым руководством сохраните и измените приведенный ниже JSON в виде файла all-vms-in-subscription-static.parameters.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource sub level metric alert via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource sub level metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetSubscription":{
            "value": "/subscriptions/replace-with-subscription-id"
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },        
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "0"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

Вы можете создать оповещение метрики с помощью шаблона и файла параметров, используя PowerShell или Azure CLI из текущего рабочего каталога.

Использование Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile all-vms-in-subscription-static.json -TemplateParameterFile all-vms-in-subscription-static.parameters.json
```

Использование Azure CLI

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file all-vms-in-subscription-static.json \
    --parameters @all-vms-in-subscription.parameters-static.json
```

### <a name="dynamic-thresholds-alert-on-all-virtual-machines-in-a-subscription"></a>Оповещение с динамическим пороговым значением на всех виртуальных машинах в подписке

Этот шаблон позволяет создать правило генерации оповещений о метрике с динамическим пороговым значением, которое отслеживает процент загрузки ЦП для всех виртуальных машин (в одном регионе Azure) в подписке.

Для работы с этим пошаговым руководством сохраните приведенный ниже JSON в виде файла all-vms-in-subscription-dynamic.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetSubscription":{
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Azure Resource Manager path up to subscription ID. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('targetSubscription')]"],
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Приведенный выше шаблон можно использовать с файлом параметров, указанным ниже.
Для работы с этим пошаговым руководством сохраните и измените приведенный ниже JSON в виде файла all-vms-in-subscription-dynamic.parameters.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource sub level metric alert with Dynamic Thresholds via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource sub level metric alert with Dynamic Thresholds created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetSubscription":{
            "value": "/subscriptions/replace-with-subscription-id"
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

Вы можете создать оповещение метрики с помощью шаблона и файла параметров, используя PowerShell или Azure CLI из текущего рабочего каталога.

Использование Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile all-vms-in-subscription-dynamic.json -TemplateParameterFile all-vms-in-subscription-dynamic.parameters.json
```

Использование Azure CLI

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file all-vms-in-subscription-dynamic.json \
    --parameters @all-vms-in-subscription-dynamic.parameter-dynamics.json
```

### <a name="static-threshold-alert-on-a-list-of-virtual-machines"></a>Оповещение со статическим пороговым значением для списка виртуальных машин

Этот шаблон позволяет создать правило генерации оповещений о метрике со статическим пороговым значением, которое отслеживает процент загрузки ЦП для списка виртуальных машин (в одном регионе Azure) в подписке.

Для работы с этим пошаговым руководством сохраните приведенный ниже JSON в виде файла list-of-vms-static.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetResourceId":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "array of Azure resource Ids. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroup/resource-group-name/Microsoft.compute/virtualMachines/vm-name"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": "[parameters('targetResourceId')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Приведенный выше шаблон можно использовать с файлом параметров, указанным ниже.
Для работы с этим пошаговым руководством сохраните и измените приведенный ниже JSON в виде файла list-of-vms-static.parameters.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource metric alert by list via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert by list created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceId":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1/Microsoft.Compute/virtualMachines/replace-with-vm-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2/Microsoft.Compute/virtualMachines/replace-with-vm-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "0"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

Вы можете создать оповещение метрики с помощью шаблона и файла параметров, используя PowerShell или Azure CLI из текущего рабочего каталога.

Использование Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile list-of-vms-static.json -TemplateParameterFile list-of-vms-static.parameters.json
```

Использование Azure CLI

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file list-of-vms-static.json \
    --parameters @list-of-vms-static.parameters.json
```

### <a name="dynamic-thresholds-alert-on-a-list-of-virtual-machines"></a>Оповещение с динамическим пороговым значением для списка виртуальных машин

Этот шаблон позволяет создать правило генерации оповещений о метрике с динамическим пороговым значением, которое отслеживает процент загрузки ЦП для списка виртуальных машин (в одном регионе Azure) в подписке.

Для работы с этим пошаговым руководством сохраните приведенный ниже JSON в виде файла list-of-vms-dynamic.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetResourceId":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "array of Azure resource Ids. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroup/resource-group-name/Microsoft.compute/virtualMachines/vm-name"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
             "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
             "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": "[parameters('targetResourceId')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Приведенный выше шаблон можно использовать с файлом параметров, указанным ниже.
Для работы с этим пошаговым руководством сохраните и измените приведенный ниже JSON в виде файла list-of-vms-dynamic.parameters.json.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource metric alert with Dynamic Thresholds by list via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert with Dynamic Thresholds by list created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceId":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1/Microsoft.Compute/virtualMachines/replace-with-vm-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2/Microsoft.Compute/virtualMachines/replace-with-vm-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

Вы можете создать оповещение метрики с помощью шаблона и файла параметров, используя PowerShell или Azure CLI из текущего рабочего каталога.

Использование Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile list-of-vms-dynamic.json -TemplateParameterFile list-of-vms-dynamic.parameters.json
```

Использование Azure CLI

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file list-of-vms-dynamic.json \
    --parameters @list-of-vms-dynamic.parameters.json
```

## <a name="template-for-an-availability-test-along-with-a-metric-alert"></a>Шаблон для теста доступности вместе с метрическим оповещением

[Тесты доступности Application Insights](../../azure-monitor/app/monitor-web-app-availability.md) помогут вам контролировать доступность вашего веб-сайта/приложения из различных мест по всему миру. Оповещения о проверке доступности уведомляют вас о сбое тестов доступности в определенном количестве мест.
Предупреждения теста доступности того же типа ресурса, что и метрические оповещения (Microsoft.Insights/metricAlerts). Следующий пример шаблона менеджера ресурсов Azure можно использовать для настройки простого теста доступности и связанного с ними оповещения.

Сохранить Json ниже, как availabilityalert.json для целей этого пошагового листа.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appName": {
      "type": "string"
    },
    "pingURL": {
      "type": "string"
    },
    "pingText": {
      "type": "string",
      "defaultValue": ""
    },
    "actionGroupId": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "variables": {
    "pingTestName": "[concat('PingTest-', toLower(parameters('appName')))]",
    "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
  },
  "resources": [
    {
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('location')]",
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 300,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('pingURL'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },
    {
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/metricAlerts",
      "apiVersion": "2018-03-01",
      "location": "global",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "description": "Alert for web test",
        "severity": 1,
        "enabled": true,
        "scopes": [
          "[resourceId('Microsoft.Insights/webtests',variables('pingTestName'))]",
          "[resourceId('Microsoft.Insights/components',parameters('appName'))]"
        ],
        "evaluationFrequency": "PT1M",
        "windowSize": "PT5M",
        "templateType": 0,
        "criteria": {
          "odata.type": "Microsoft.Azure.Monitor.WebtestLocationAvailabilityCriteria",
          "webTestId": "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]",
          "componentId": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
          "failedLocationCount": 2
        },
        "actions": [
          {
            "actionGroupId": "[parameters('actionGroupId')]"
          }
        ]
      }
    }
  ]
}
```

Вы можете задать значения для параметров с помощью командной строки или файла параметров. Ниже приведен пример файла параметров.


> [!NOTE]
>
> `&amp`; является ссылкой на сущность HTML для &. Параметры URL-адреса по-прежнему разделены одним &, но если вы упоминаете URL в HTML, необходимо кодировать его. Таким образом, если у вас есть какие-либо "&" в`&amp`вашем pingURL параметр значение, вы должны избежать его с ";"

Сохраните Json ниже, как availabilityalert.parameters.json и изменить его по мере необходимости.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appName": {
            "value": "Replace with your Application Insights resource name"
        },
        "pingURL": {
            "value": "https://www.yoursite.com"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/microsoft.insights/actiongroups/replace-with-action-group-name"
        },
        "location": {
            "value": "Replace with the location of your Application Insights resource"
        }
    }
}
```

Вы можете создать тест доступности и связанное с ним оповещение с помощью файла шаблона и параметров с помощью PowerShell или Azure CLI.

Использование Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AvailabilityAlertDeployment -ResourceGroupName ResourceGroupofApplicationInsightsComponent `
  -TemplateFile availabilityalert.json -TemplateParameterFile availabilityalert.parameters.json
```

Использование Azure CLI

```azurecli
az login

az group deployment create \
    --name AvailabilityAlertDeployment \
    --resource-group ResourceGroupofApplicationInsightsComponent \
    --template-file availabilityalert.json \
    --parameters @availabilityalert.parameters.json
```

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения об [оповещениях в Azure](alerts-overview.md)
- Сведения о [создании группы действий с помощью шаблона Resource Manager](action-groups-create-resource-manager-template.md)
- Синтаксис и свойства JSON см. в справочнике по шаблонам [Microsoft.Insights/metricAlerts](/azure/templates/microsoft.insights/metricalerts).
