---
title: Указание настраиваемого образа в шаблоне масштабируемого набора Azure | Документация Майкрософт
description: Узнайте, как добавить настраиваемый образ в существующий шаблон масштабируемого набора виртуальных машин Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: manayar
ms.openlocfilehash: 2415d0dc2b9a2c4229d9910b42eb8ec9309ac7a7
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869111"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Добавление настраиваемого образа в шаблон масштабируемого набора Azure

В этой статье показано, как изменить [простой масштабируемый набор шаблона](virtual-machine-scale-sets-mvss-start.md) для развертывания на основе пользовательского образа.

## <a name="change-the-template-definition"></a>Изменение определения шаблона
В [предыдущей статье](virtual-machine-scale-sets-mvss-start.md) мы создали простой масштабируемый набор шаблона. Теперь будет использовать этого более раннего шаблона и изменить его, чтобы создать шаблон, который развертывает масштабируемый набор с помощью пользовательского образа.  

### <a name="creating-a-managed-disk-image"></a>Создание образа управляемого диска

Если вы уже создали настраиваемый образ управляемого диска (ресурс типа `Microsoft.Compute/images`), можете пропустить этот раздел.

Сначала добавьте параметр `sourceImageVhdUri`. Это URI универсального большого двоичного объекта в службе хранилища Azure, который содержит настраиваемый образ для развертывания.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Затем добавьте ресурс типа `Microsoft.Compute/images`. Это образ управляемого диска на основе универсального большого двоичного объекта, который расположен в URI `sourceImageVhdUri`. Этот образ должен находиться в том же регионе, что и масштабируемый набор, который его использует. В свойствах образа укажите тип ОС, расположение большого двоичного объекта (из параметра `sourceImageVhdUri`) и тип учетной записи хранения:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

Добавьте в ресурс масштабируемого набора предложение `dependsOn` со ссылкой на настраиваемый образ. Это позволит создать образ до того, как масштабируемый набор попытается выполнить развертывание из этого образа:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Изменение свойств масштабируемого набора для использования управляемого образа

В свойстве `imageReference` масштабируемого набора `storageProfile` вместо издателя, предложения, номера SKU и версии образа платформы укажите `id` ресурса `Microsoft.Compute/images`:

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

В этом примере используется функция `resourceId`, чтобы получить идентификатор ресурса образа, созданного в том же шаблоне. Если образ управляемого диска создан заранее, укажите идентификатор этого образа. Идентификатор должен быть представлен в следующем формате: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Следующие шаги

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
