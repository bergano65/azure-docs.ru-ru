---
title: Использование автоматического масштабирования Azure с метриками гостевой виртуальной машины в шаблоне масштабируемого набора Linux | Документация Майкрософт
description: Узнайте, как выполнять автоматическое масштабирование, используя метрики гостевой виртуальной машины в шаблоне масштабируемого набора виртуальных машин Linux
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8cd665ffd82547c4f554eb4a515a8da7dc5b3f5f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868990"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Автоматическое масштабирование с помощью метрик гостевой виртуальной машины в шаблоне масштабируемого набора Linux

Существует два широко известных типа метрики в Azure, которые собираются на основе виртуальных машин и масштабируемых наборов: Метрики узла и метрики гостевой виртуальной машины. На высоком уровне Если вы хотите использовать стандартный ЦП, диска и сети, метрики узла могут удачным выбором. Если, однако вам требуется больший набор показателей, метрики гостевой виртуальной машины необходимо просматривать в.

Метрики узла не требуют дополнительной настройки, так как они собираются с узла виртуальной Машины, а метрики гостевой виртуальной машины необходимо установить [расширение системы диагностики Windows Azure](../virtual-machines/windows/extensions-diagnostics-template.md) или [расширение системы диагностики Azure в Linux ](../virtual-machines/linux/diagnostic-extension.md) на гостевой виртуальной Машине. Одна из основных причин использования метрик гостевой виртуальной машины вместо метрик узла заключается в том, что метрики гостевой виртуальной машины предоставляют больший набор показателей, чем метрики узла. Один из таких примеров — это метрики потребления памяти, которые доступны только через метрики гостевой виртуальной машины. Поддерживаемые метрики узла перечислены [здесь](../azure-monitor/platform/metrics-supported.md), а часто используемые метрики гостевой виртуальной машины — [здесь](../azure-monitor/platform/autoscale-common-metrics.md). В этой статье показано, как изменить [шаблон основные приемлемого масштабируемого набора](virtual-machine-scale-sets-mvss-start.md) использовать правила автомасштабирования, в зависимости от метрики гостевой виртуальной машины для масштабируемых наборов Linux.

## <a name="change-the-template-definition"></a>Изменение определения шаблона

В [предыдущей статье](virtual-machine-scale-sets-mvss-start.md) мы создали простой масштабируемый набор шаблона. Теперь будет использовать этот шаблон более ранней и изменить его, чтобы создать шаблон, который развертывает масштабируемый набор Linux с автоматическим масштабированием на основе метрик гостевой.

Сначала добавьте параметры для `storageAccountName` и `storageAccountSasToken`. Агент диагностики хранит данные метрик в [таблице](../cosmos-db/table-storage-how-to-use-dotnet.md) в этой учетной записи хранения. Начиная с агента диагностики Linux версии 3.0 использование ключа доступа к хранилищу больше не поддерживается. Вместо этого используйте [маркер SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Затем измените масштабируемый набор `extensionProfile`, чтобы включить расширение диагностики. В этой конфигурации укажите идентификатор ресурса масштабируемого набора, который будет использоваться для сбора метрик, а также учетную запись хранения и маркер SAS для хранения метрик. Укажите то, как часто будут вычисляться метрики (в данном случае — каждую минуту) и какие метрики нужно отслеживать (в этом случае — это процент используемой памяти). Дополнительные сведения об этой конфигурации и других метриках см. в [этой документации](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Наконец, добавьте ресурс `autoscaleSettings`, чтобы настроить автоматическое масштабирование на основе этих метрик. Для ресурса указано предложение `dependsOn`, которое ссылается на масштабируемый набор, чтобы убедиться, что масштабируемый набор имеется, прежде чем пытаться его масштабировать. Если бы для автоматического масштабирования была выбрана другая метрика, вы бы использовали `counterSpecifier` из конфигурации расширения диагностики в качестве `metricName` в конфигурации автоматического масштабирования. Дополнительные сведения о настройке автомасштабирования см. в статье [Рекомендации по автомасштабированию](../azure-monitor/platform/autoscale-best-practices.md) и [Создание или обновление параметров автоматического масштабирования](/rest/api/monitor/autoscalesettings).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
