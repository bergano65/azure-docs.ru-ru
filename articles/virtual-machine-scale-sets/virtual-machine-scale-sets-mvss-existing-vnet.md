---
title: Ссылка на существующую виртуальную сеть в шаблоне масштабируемого набора Azure
description: Узнайте, как добавить виртуальную сеть в существующий шаблон масштабируемого набора виртуальных машин Azure.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: e725e75b8b19fd8b3295226639b5e5aeb3736e34
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275534"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Добавление ссылки на существующую виртуальную сеть в шаблон масштабируемого набора Azure

В этой статье показано, как изменить [шаблон базового масштабируемого набора](virtual-machine-scale-sets-mvss-start.md) для развертывания в существующей виртуальной сети вместо создания новой.

## <a name="change-the-template-definition"></a>Изменение определения шаблона

В [предыдущей статье](virtual-machine-scale-sets-mvss-start.md) мы создали шаблон базового масштабируемого набора. Теперь мы будем использовать этот шаблон и изменим его, чтобы создать шаблон, который развертывает масштабируемый набор в существующей виртуальной сети. 

Сначала добавьте параметр `subnetId`. Эта строка будет передана в конфигурацию масштабируемого набора, благодаря чему он сможет идентифицировать предварительно созданную подсеть для развертывания виртуальных машин. Эта строка должна иметь следующий вид: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Например, чтобы развернуть масштабируемый набор в существующей виртуальной сети с именем `myvnet`, подсети `mysubnet`, группе ресурсов `myrg` и подписке `00000000-0000-0000-0000-000000000000`, идентификатор подсети должен быть таким: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Затем удалите ресурс виртуальной сети из массива `resources`, потому что используется существующая виртуальная сеть и не требуется развертывать новую.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

Виртуальная сеть уже существовала до развертывания шаблона, поэтому нет необходимости указывать предложение dependsOn из масштабируемого набора для виртуальной сети. Удалите следующие строки:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Наконец, передайте параметр `subnetId`, заданный пользователем (вместо того, чтобы использовать `resourceId` для получения идентификатора виртуальной сети в том же развертывании, что делает шаблон базового приемлемого масштабируемого набора).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
