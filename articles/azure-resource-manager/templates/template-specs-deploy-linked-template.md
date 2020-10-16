---
title: Развертывание спецификации шаблона в качестве связанного шаблона
description: Узнайте, как развернуть существующую спецификацию шаблона в связанном развертывании.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 4469e793a7da407f793bfe2885f7bb039e29d736
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91369116"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Руководство. Развертывание спецификации шаблона в качестве связанного шаблона (Предварительная версия)

Узнайте, как развернуть существующую [спецификацию шаблона](template-specs.md) с помощью [связанного развертывания](linked-templates.md#linked-template). Спецификации шаблонов можно использовать для совместного использования шаблонов ARM с другими пользователями в Организации. После создания спецификации шаблона можно развернуть спецификацию шаблона с помощью Azure PowerShell или Azure CLI. Вы также можете развернуть спецификацию шаблона как часть решения с помощью связанного шаблона.

## <a name="prerequisites"></a>Предварительные требования

Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.

> [!NOTE]
> Сейчас спецификации шаблонов доступны в предварительной версии. Чтобы использовать его, необходимо [зарегистрироваться для использования предварительной версии](https://aka.ms/templateSpecOnboarding).

## <a name="create-a-template-spec"></a>Создание спецификации шаблона

Следуйте инструкциям в [кратком руководстве по созданию и развертыванию шаблона спецификации](quickstart-create-template-specs.md) , чтобы создать шаблон спецификации для развертывания учетной записи хранения. В следующем разделе вам потребуется имя группы ресурсов для спецификации шаблона, имя спецификации шаблона и версию спецификации шаблона.

## <a name="create-the-main-template"></a>Создание основного шаблона

Чтобы развернуть шаблон спецификации в шаблоне ARM, добавьте [ресурс развертывания](/azure/templates/microsoft.resources/deployments) в основной шаблон. В `templateLink` свойстве укажите идентификатор ресурса спецификации шаблона. Создайте шаблон со следующим JSON с именем **azuredeploy.jsв**. В этом учебнике предполагается, что вы сохранили путь в **c:\Templates\deployTS\azuredeploy.js** , но можете использовать любой путь.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

Идентификатор спецификации шаблона создается с помощью [`resourceID()`](template-functions-resource.md#resourceid) функции. Аргумент группы ресурсов в функции resourceID () является необязательным, если Темплатеспек находится в той же группе ресурсов текущего развертывания.  Можно также напрямую передать идентификатор ресурса в качестве параметра. Чтобы получить идентификатор, используйте:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Существует известная проблема при получении идентификатора спецификации шаблона и ее присвоении переменной в Windows PowerShell.

---

Для передачи параметров в спецификацию шаблона используется следующий синтаксис:

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> ApiVersion `Microsoft.Resources/deployments` должен быть 2020-06-01 или более поздней версии.

## <a name="deploy-the-template"></a>Развертывание шаблона

При развертывании связанного шаблона он развертывает как веб-приложение, так и учетную запись хранения. Развертывание аналогично развертыванию других шаблонов ARM.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json"

```

---

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о создании спецификации шаблона, включающей связанные шаблоны, см. в статье [Создание спецификации для связанного шаблона](template-specs-create-linked.md).
