---
title: Расположение ресурсов шаблона
description: Описывает, как установить местоположение ресурсов в шаблоне управления ресурсами Azure.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: a8324dac1232eecd5624e5f1dc0e6656295c0a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156435"
---
# <a name="set-resource-location-in-arm-template"></a>Установка местоположения ресурсов в шаблоне ARM

При развертывании шаблона Azure Resource Manager (ARM) необходимо учесть местоположение для каждого ресурса. Местоположение не должно быть таким же местом, как расположение группы ресурсов.

## <a name="get-available-locations"></a>Упомяните доступные места

Различные типы ресурсов поддерживаются в разных расположениях. Чтобы получить поддерживаемые местоположения для типа ресурсов, используйте Azure PowerShell или Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Использование параметра местоположения

Чтобы обеспечить гибкость при развертывании шаблона, используйте параметр для указания местоположения ресурсов. Установите значение параметра `resourceGroup().location`по умолчанию.

В следующем примере показана учетная запись хранения, которая развертывается в расположении, указанном как параметр:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Полный список функций шаблонов см. в статье [Функции шаблонов диспетчера ресурсов Azure](template-functions.md).
* Для получения дополнительной информации о файлах [шаблонов см.](template-syntax.md)
