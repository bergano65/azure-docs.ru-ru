---
title: Шаблон Azure Resource Manager для Log Analytics рабочей области
description: Шаблоны Azure Resource Manager вы можете применить для создания и настройки рабочих областей Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2020
ms.openlocfilehash: 240a261f8dd401f36ef763e4c1274a1c0760f2dd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515654"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Управление рабочей областью Log Analytics с помощью шаблонов Azure Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Шаблоны Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) можно использовать для создания и настройки log Analytics рабочих областей в Azure Monitor. Примеры задач, которые можно выполнять с помощью шаблонов.

* Создание рабочей области, включая настройку ценовой категории и резервирования емкости
* Добавление решения
* Создание сохраненных поисковых запросов
* Создание группы компьютеров
* Включение сбора журналов IIS с компьютеров, на которых установлен агент Windows
* Сбор счетчиков производительности с компьютеров под управлением Linux и Windows
* Сбор событий из системного журнала с компьютеров Linux 
* Сбор событий из журналов событий Windows
* Получение пользовательских журналов с компьютера Windows
* Добавление агента Log Analytics на виртуальную машину Azure
* Настройка Log Analytics для индексирования данных, собранных системой диагностики Azure

В статье приведены примеры кода, иллюстрирующие некоторые конфигурации, которые можно выполнить с помощью шаблонов.

## <a name="api-versions"></a>Версии API

В следующей таблице перечислены версии API для ресурсов, используемых в этом примере.

| Ресурс | Тип ресурса | Версия API |
|:---|:---|:---|
| Рабочая область   | workspaces    | 2017-03-15-preview |
| Поиск      | savedSearches | 2015-03-20 |
| Источник данных | datasources   | 2015-11-01-preview |
| Решение    | solutions     | 2015-11-01-preview |

## <a name="create-a-log-analytics-workspace"></a>Создание рабочей области Log Analytics

В следующем примере создается рабочая область с помощью шаблона на локальном компьютере. Шаблон JSON настроен так, что требуется только имя и расположение новой рабочей области. Он использует значения, заданные для других параметров рабочей области, таких как [режим управления доступом](design-logs-deployment.md#access-control-mode), ценовая категория, срок хранения и уровень резервирования емкости.

> [!WARNING]
> Следующий шаблон создает рабочую область Log Analytics и настраивает сбор данных. Это может привести к изменению параметров выставления счетов. Ознакомьтесь [со сведениями об управлении использованием и затратами Azure Monitor](manage-cost-storage.md) для получения сведений о выставлении счетов за данные, собранные в log Analytics рабочей области, перед их применением в среде Azure.

Для резервирования емкости необходимо определить выбранное резервирование емкости для приема данных, указав номер SKU `CapacityReservation` и значение в ГБ для свойства `capacityReservationLevel` . В следующем списке приведены поддерживаемые значения и поведение при его настройке.

- После установки лимита резервирования нельзя изменить номер SKU в течение 31 дня.

- После установки значения резервирования его можно увеличить только в течение 31 дня.

- Значение можно задать только `capacityReservationLevel` в кратных 100 с максимальным значением 50000.

- При увеличении уровня резервирования таймер сбрасывается, и его нельзя изменить в течение еще 31 дня после этого обновления.  

- Если изменить любое другое свойство для рабочей области, но сохранили лимит резервирования на том же уровне, таймер не сбрасывается. 

### <a name="create-and-deploy-template"></a>Создание и развертывание шаблона

1. Скопируйте и вставьте в него следующий синтаксис JSON:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
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
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
        }
      },
      "location": {
        "type": "String",
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
                    "name": "[parameters('sku')]"
                },
                "retentionInDays": 120,
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
          }
       ]
    }
    ```

   >[!NOTE]
   >Для параметров резервирования емкости используйте следующие свойства в разделе "SKU":
   >* "Name": "КапаЦитиресерватион",
   >* "КапаЦитиресерватионлевел": 100

2. Отредактируйте шаблон с учетом ваших требований. Вместо встраивания параметров в строку запуска попробуйте создать [файл параметров Resource Manager](../../azure-resource-manager/templates/parameter-files.md). Просмотрите справочник по [шаблону Microsoft.OperationalInsights/workspaces](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) с описанием поддерживаемых свойств и значений. 

3. Сохраните этот файл как **deploylaworkspacetemplate.json** в локальной папке.

4. Теперь вы можете развернуть этот шаблон. Для создания рабочей области можно использовать PowerShell или командную строку, указав имя рабочей области и расположение в составе команды. Имя рабочей области должно быть глобально уникальным во всех подписках Azure.

   * Для PowerShell используйте следующие команды из папки с шаблоном.
   
        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json -workspaceName <workspace-name> -location <location>
        ```

   * Для командной строки используйте следующие команды из папки с шаблоном.

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json --workspaceName <workspace-name> --location <location>
        ```

Развертывание может занять несколько минут. По завершении выполнения появится сообщение с результатами наподобие приведенного ниже.<br><br> ![Пример результатов по завершении развертывания](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Настройка рабочей области Log Analytics

Этот пример шаблона иллюстрирует следующие задачи.

1. Добавление решений в рабочую область
2. Создание сохраненных поисков. Чтобы обеспечить случайное переопределение развертываний сохраненных поисков, необходимо добавить свойство eTag в ресурс "Саведсеарчес", чтобы переопределить и поддерживать идемпотентности сохраненных поисков.
3. Создайте сохраненную функцию. Тег eTag должен быть добавлен к функции переопределения и поддерживать идемпотентности.
4. Создание группы компьютеров
5. Включение сбора журналов IIS с компьютеров, на которых установлен агент Windows
6. Сбор счетчиков производительности логического диска с компьютеров под управлением Linux ("Процент использования индексных дескрипторов"; "Свободно мегабайт"; "Процент используемого места"; "Количество обращений к диску (в секунду)"; "Количество обращений чтения или записи (в секунду))"
7. Сбор событий из системного журнала с компьютеров Linux
8. Сбор событий (ошибок и предупреждений) из журнала событий приложений с компьютеров Windows
9. Сбор данных счетчика производительности "Доступный объем памяти" (в МБ) с компьютеров Windows
10. Сбор журналов IIS и журналов событий Windows, которые система диагностики Azure записывает в учетную запись хранилища
11. Получение пользовательских журналов с компьютера Windows

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
        "PerGB2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "defaultValue": "pergb2018",
      "metadata": {
        "description": "Pricing tier: pergb2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
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
        "description": "If set to true, changing retention to 30 days will immediately delete older data. Use this with extreme caution. This only applies when retention is being set to 30 days."
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
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the storage account with Azure diagnostics output"
      }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
      "type": "string",
      "metadata": {
        "description": "The resource group name containing the storage account with Azure diagnostics output"
      }
    },
    "customLogName": {
      "type": "string",
      "metadata": {
        "description": "The custom log name"
      }
    }
  },
  "variables": {
    "Updates": {
      "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "Updates"
    },
    "AntiMalware": {
      "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "AntiMalware"
    },
    "SQLAssessment": {
      "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "SQLAssessment"
    },
    "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2017-03-15-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "retentionInDays": "[parameters('dataRetention')]",
        "features": {
          "immediatePurgeDataOn30Days": "[parameters('immediatePurgeDataOn30Days')]"
        },
        "sku": {
          "name": "[parameters('sku')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-03-20",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "eTag": "*",
            "category": "VMSS",
            "displayName": "VMSS Instance Count",
            "query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "version": 1
          }
        },
        {
          "apiVersion": "2017-04-26-preview",
          "name": "Cross workspace function",
          "type": "savedSearches",
            "dependsOn": [
             "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "properties": {
              "etag": "*",
              "displayName": "failedLogOnEvents",
              "category": "Security",
              "FunctionAlias": "failedlogonsecurityevents",
              "query": "
                union withsource=SourceWorkspace
                workspace('workspace1').SecurityEvent,
                workspace('workspace2').SecurityEvent,
                workspace('workspace3').SecurityEvent,
                | where EventID == 4625"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslog",
          "properties": {
            "syslogName": "kern",
            "syslogSeverities": [
              {
                "severity": "emerg"
              },
              {
                "severity": "alert"
              },
              {
                "severity": "crit"
              },
              {
                "severity": "err"
              },
              {
                "severity": "warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
            "performanceCounters": [
              {
                "counterName": "% Used Inodes"
              },
              {
                "counterName": "Free Megabytes"
              },
              {
                "counterName": "% Used Space"
              },
              {
                "counterName": "Disk Transfers/sec"
              },
              {
                "counterName": "Disk Reads/sec"
              },
              {
                "counterName": "Disk Writes/sec"
              }
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "dataSources",
          "name": "[concat(parameters('workspaceName'), parameters('customLogName'))]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLog",
          "properties": {
            "customLogName": "[parameters('customLogName')]",
            "description": "this is a description",
            "extractions": [
              {
                "extractionName": "TimeGenerated",
                "extractionProperties": {
                  "dateTimeExtraction": {
                    "regex": [
                      {
                        "matchIndex": 0,
                        "numberdGroup": null,
                        "pattern": "((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]"
                      }
                    ]
                  }
                },
                "extractionType": "DateTime"
              }
            ],
            "inputs": [
              {
                "location": {
                  "fileSystemLocations": {
                    "linuxFileTypeLogPaths": null,
                    "windowsFileTypeLogPaths": [
                      "[concat('c:\\Windows\\Logs\\',parameters('customLogName'))]"
                    ]
                  }
                },
                "recordDelimiter": {
                  "regexDelimiter": {
                    "matchIndex": 0,
                    "numberdGroup": null,
                    "pattern": "(^.*((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9].*$)"
                  }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-03-20",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [
              "wad-iis-logfiles"
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "sku": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "immediatePurgeDataOn30Days": {
      "type": "bool",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').features.immediatePurgeDataOn30Days]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}
```

### <a name="deploying-the-sample-template"></a>Развертывание примера шаблона

Чтобы развернуть этот шаблон, запустите следующую команду:

1. Сохраните прилагаемый пример в файл, например с именем `azuredeploy.json` 
2. Измените шаблон так, чтобы получить нужную конфигурацию
3. Разверните итоговый шаблон с помощью PowerShell или командной строки

#### <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Командная строка

```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Примеры шаблонов Resource Manager

Коллекция шаблонов Azure позволяет быстро начать работу, применяя предложенные шаблоны для Log Analytics, в том числе перечисленные ниже.

* [Развертывание виртуальной машины под управлением Windows с расширением Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Развертывание виртуальной машины под управлением Linux с расширением Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Мониторинг Azure Site Recovery с использованием существующей рабочей области Log Analytics](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Мониторинг веб-приложений Azure с использованием существующей рабочей области Log Analytics](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Добавление существующей учетной записи хранения в Log Analytics](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание агента Windows на виртуальных машинах Azure с помощью шаблона Resource Manager](../../virtual-machines/extensions/oms-windows.md)

* [Развертывание агента Linux на виртуальных машинах Azure с помощью шаблона Resource Manager](../../virtual-machines/extensions/oms-linux.md)
