---
title: Создание оповещений о метриках для журналов в Azure Monitor
description: Руководство по созданию оповещений о метриках практически в реальном времени на основе популярных данных Log Analytics.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: ac4391b91d818b21e392e134115294fb84473e69
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449663"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Создание оповещений о метриках для журналов в Azure Monitor

## <a name="overview"></a>Обзор

Azure Monitor поддерживает [тип оповещений о метриках](../../azure-monitor/platform/alerts-metric-near-real-time.md), который обладает преимуществами над [классическими оповещениями](../../azure-monitor/platform/alerts-classic-portal.md). Метрики доступны для [большого числа служб Azure](../../azure-monitor/platform/metrics-supported.md). В этой статье описывается использование подмножества для ресурса `Microsoft.OperationalInsights/workspaces`.

Можно использовать оповещения о метриках на основе популярных журналов Log Analytics, извлекаемых в виде метрик в составе метрик из журналов, включая ресурсы Azure или локальной среды. Поддерживаемые решения Log Analytics перечислены ниже.

- [Счетчики производительности](../../azure-monitor/platform/data-sources-performance-counters.md) для компьютеров Windows и Linux.
- [Записи пульсов для решения "Работоспособность агентов"](../../azure-monitor/insights/solution-agenthealth.md).
- Записи [управления обновлениями](../../automation/automation-update-management.md).
- Журналы [данных событий](../../azure-monitor/platform/data-sources-windows-events.md)

Существует много преимуществ использования **оповещений о метриках для журналов** по сравнению с [оповещениями журналов](../../azure-monitor/platform/alerts-log.md) на основе запросов в Azure. Некоторые из них перечислены ниже.

- Оповещения о метриках обеспечивают возможность мониторинга почти в реальном времени, а оповещения о метриках для журналов обеспечивают то же самое путем ответвления данных из источника журнала.
- Оповещения о метриках учитывают состояния, уведомляя только один раз при срабатывании оповещения и один раз при его обработке, в отличие от оповещений журналов, в которых состояние не учитывается и которые срабатывают с установленным интервалом при соответствии условиям оповещения.
- Оповещения о метриках для журнала предоставляют множество измерений, позволяя выполнять фильтрацию по конкретным значениям, таким как "Компьютеры", "Тип ОС" и т. д., без необходимости писать запрос в средстве аналитики.

> [!NOTE]
> Конкретная метрика и (или) измерение отображается, только если для него есть данные за выбранный период. Эти метрики доступны для клиентов с рабочими областями Azure Log Analytics.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Метрики и измерения, поддерживаемые для журналов

 Оповещения о метриках поддерживают оповещения для метрик, использующих измерения. Измерения можно использовать для фильтрации метрик до необходимого уровня. Вот полный список метрик, поддерживаемых для журналов из [рабочих областей Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces), для всех поддерживаемых решений.

> [!NOTE]
> Чтобы просмотреть поддерживаемые метрики, извлекаемые из рабочей области Log Analytics с помощью [обозревателя метрик Azure Monitor](../../azure-monitor/platform/metrics-charts.md), необходимо создать оповещение о метрике для журнала для указанной метрики. Измерения, выбранные в оповещении о метрике для журналов, будут отображаться только для просмотра с помощью обозревателя метрик Azure Monitor.

## <a name="creating-metric-alert-for-log-analytics"></a>Создание оповещения о метрике для Log Analytics

Данные метрик из популярных журналов перед обработкой в Log Analytics передаются по конвейеру в обозреватель метрик Azure Monitor. Это позволяет пользователям задействовать возможности платформы метрик, а также оповещения о метриках — включая оповещения с частотой вплоть до 1 минуты.
Ниже перечислены средства создания оповещения о метрике для журналов.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Необходимые условия для оповещения о метрике для журналов

Чтобы обеспечить работу метрики для журналов, собранных на основе данных Log Analytics, необходимо настроить и сделать доступными следующие компоненты:

1. **Активная рабочая область Log Analytics**. Требуется действительная и активная рабочая область Log Analytics. Дополнительные сведения см. в статье [Создание рабочей области Log Analytics на портале Azure](../../azure-monitor/learn/quick-create-workspace.md).
2. **Настроенный агент для рабочей области Log Analytics**. Агент необходимо настроить для виртуальных машин Azure и (или) локальных виртуальных машин для отправки данных в рабочую область Log Analytics, указанную в предыдущем шаге. Дополнительные сведения см. в статье [Обзор агентов Azure для мониторинга виртуальных машин Azure](../../azure-monitor/platform/agents-overview.md).
3. **Установленные поддерживаемые решения Log Analytics**. Настроенное решение Log Analytics должно отправлять данные в рабочую область Log Analytics. Поддерживаемые решения: [счетчики производительности для Windows и Linux](../../azure-monitor/platform/data-sources-performance-counters.md), [записи пульса для решения "Работоспособность агентов"](../../azure-monitor/insights/solution-agenthealth.md), "Управление обновлениями" и [данные событий](../../azure-monitor/platform/data-sources-windows-events.md).
4. **Решения Log Analytics, настроенные для отправки журналов**. В решении Log Analytics должны быть включены необходимые журналы (данные), соответствующие [метрикам, поддерживаемым для рабочих областей Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces). Например, для подсчета *% доступной памяти* соответствующий счетчик сначала необходимо настроить в решении [Счетчики производительности](../../azure-monitor/platform/data-sources-performance-counters.md).

## <a name="configuring-metric-alert-for-logs"></a>Настройка оповещения о метриках для журналов

 Создавать оповещения о метриках и управлять ими можно с помощью портала Azure, шаблонов Resource Manager, REST API, PowerShell и Azure CLI. Так как оповещения о метриках для журналов являются разновидностью оповещений о метриках, после выполнения необходимых условий можно создать оповещение о метрике для журналов для указанной рабочей области Log Analytics. Все характеристики и функциональные возможности [оповещений о метриках](../../azure-monitor/platform/alerts-metric-near-real-time.md) будут применимы и к оповещениям о метриках для журналов, включая схему полезных данных, применимые квоты и выставляемую в счете цену.

Пошаговые инструкции и примеры см. в статье о [создании оповещений о метриках и управлении ими](https://aka.ms/createmetricalert). Что касается оповещений о метриках для журналов, следуйте инструкциям по управлению оповещениями о метриках и обеспечьте выполнение следующих условий:

- Целевым объектом для оповещения о метрике должна быть действующая *рабочая область Log Analytics*.
- Сигнал, выбранный для оповещения о метрике для указанной *рабочей области Log Analytics*, должен относиться к типу **Метрика**.
- Выполните фильтрацию по определенным условиям или ресурсу с помощью фильтров измерений. Метрики для журналов являются многомерными.
- При настройке *логики сигналов* можно создать одно оповещение для охвата нескольких значений измерения (например, "Компьютер").
- Если портал Azure **не** используется для создания оповещения о метрике для выбранной *рабочей области Log Analytics*, пользователь должен сначала вручную создать явное правило преобразования данных журнала в метрику с помощью средства Azure Monitor [Правила запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> При создании оповещения о метрике для рабочей области Log Analytics через портал Azure автоматически создается соответствующее правило для преобразования данных журнала в метрику с помощью средства Azure Monitor [Правила запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) в фоновом режиме, *без необходимости вмешательства или действий пользователя*. Для создания оповещений о метриках для журналов с использованием средств, отличных от портала Azure, см. раздел [Шаблон ресурса для оповещений о метриках для журналов](#resource-template-for-metric-alerts-for-logs) с примером средства создания правила преобразования журнала на основе ScheduledQueryRule в метрику до создания оповещения о метрике — иначе для созданного оповещения о метрике для журналов не будет данных.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Шаблон ресурса для оповещений о метриках для журналов

Как уже говорилось ранее, процесс создания оповещений о метриках из журналов состоит из двух направлений:

1. Создание правила для извлечения метрик из поддерживаемых журналов с помощью API-интерфейса scheduledQueryRule.
2. Создание оповещения о метрике, извлеченной из журнала (на шаге 1), с рабочей областью Log Analytics в качестве целевого ресурса

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Оповещения о метриках для журналов со статическим пороговым значением

Для достижения такого же результата можно использовать приведенный ниже пример шаблона Azure Resource Manager, где создание оповещения о метрике со статическим пороговым значением зависит от успешного создания правила извлечения метрик из журналов с помощью scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
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
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
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
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
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

Предположим, что приведенный выше код JSON сохранен в файле metricfromLogsAlertStatic.json. Так его можно связать с файлом параметров JSON для создания на основе шаблона ресурса. Пример файла параметров JSON приведен ниже:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

При условии, что указанный выше файл параметров сохранен с именем metricfromLogsAlertStatic.parameters.json, можно создать оповещение о метрике для журналов с помощью [шаблона ресурса для создания на портале Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

Вместо этого можно также использовать следующую команду Azure PowerShell:

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Также можно развернуть шаблон ресурса с помощью интерфейса командной строки Azure:

```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Оповещения о метриках для журналов с динамическими пороговыми значениями

Для достижения такого же результата можно использовать приведенный ниже пример шаблона Azure Resource Manager, где создание оповещения о метрике с динамическим пороговым значением зависит от успешного создания правила извлечения метрик из журналов с помощью scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
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
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
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
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
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

Предположим, что приведенный выше код JSON сохранен в файле metricfromLogsAlertDynamic.json. Так его можно связать с файлом параметров JSON для создания на основе шаблона ресурса. Пример файла параметров JSON приведен ниже:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
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
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

При условии, что указанный выше файл параметров сохранен с именем metricfromLogsAlertDynamic.parameters.json, можно создать оповещение о метрике для журналов с помощью [шаблона ресурса для создания на портале Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

Вместо этого можно также использовать следующую команду Azure PowerShell:

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Также можно развернуть шаблон ресурса с помощью интерфейса командной строки Azure:

```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения об [оповещениях о метриках](alerts-metric.md).
- Ознакомьтесь со сведениями об [оповещениях журналов в Azure](../../azure-monitor/platform/alerts-unified-log.md).
- [Подробнее об оповещениях в Azure](alerts-overview.md)
