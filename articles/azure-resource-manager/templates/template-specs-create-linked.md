---
title: Создание спецификации шаблона со связанными шаблонами
description: Узнайте, как создать спецификацию шаблона со связанными шаблонами.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: c2648cb8a71be709406f314d02a226ed097be6f0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097733"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Учебник. Создание спецификации шаблона со связанными шаблонами (Предварительная версия)

Узнайте, как создать [спецификацию шаблона](template-specs.md) со [связанным шаблоном](linked-templates.md#linked-template). Спецификации шаблонов можно использовать для совместного использования шаблонов ARM с другими пользователями в Организации. В этой статье показано, как создать спецификацию шаблона для упаковки основного шаблона и связанных с ним шаблонов с помощью нового `relativePath` свойства [ресурса развертывания](/azure/templates/microsoft.resources/deployments).

## <a name="prerequisites"></a>Предварительные требования

Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.

> [!NOTE]
> Спецификации шаблонов в настоящее время находятся на этапе предварительной версии. Чтобы использовать его, необходимо [зарегистрироваться для использования предварительной версии](https://aka.ms/templateSpecOnboarding).

## <a name="create-linked-templates"></a>Создание связанных шаблонов

Создайте основной шаблон и связанный шаблон.

Чтобы связать шаблон, добавьте [ресурс развертывания](/azure/templates/microsoft.resources/deployments) в основной шаблон. В `templateLink` свойстве укажите относительный путь к связанному шаблону в соответствии с путем к родительскому шаблону.

Связанный шаблон называется **linkedTemplate.jsв**и хранится во вложенной папке с именем **артефакты** в пути, где хранится основной шаблон.  Для параметра relativePath можно использовать одно из следующих значений:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

Если имеется другой linkedTemplate2.js, вызванный из linkedTemplate.jsв и linkedTemplate2.jsв, хранится в той же вложенной папке артефактов, relativePath, заданный в linkedTemplate.json, **linkedTemplate2.json**.

1. Создайте основной шаблон со следующим JSON. Сохраните основной шаблон как **azuredeploy.jsна** локальном компьютере. В этом учебнике предполагается, что вы сохранили путь в **c:\Templates\linkedTS\azuredeploy.js** , но можете использовать любой путь.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
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
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > ApiVersion `Microsoft.Resources/deployments` должен быть 2020-06-01 или более поздней версии.

1. Создайте каталог с именем **артефакты** в папке, в которой сохранен основной шаблон.
1. Создайте связанный шаблон со следующим JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
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

1. Сохраните шаблон как **linkedTemplate.js** в папке **артефакты** .

## <a name="create-template-spec"></a>Создать спецификацию шаблона

Спецификации шаблонов хранятся в группах ресурсов.  Создайте группу ресурсов, а затем создайте шаблон спецификации со следующим скриптом. Название спецификации шаблона — «имя- **Спецификация**».

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -ResourceGroupName templateSpecRG `
  -Name webSpec `
  -Version "1.0.0.0" `
  -Location westus2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

По завершении можно просмотреть спецификацию шаблона из портал Azure или с помощью следующего командлета:

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

## <a name="deploy-template-spec"></a>Развертывание спецификации шаблона

Теперь можно развернуть спецификацию шаблона. Развертывание спецификации шаблона выполняется так же, как и при развертывании содержащего его шаблона, за исключением того, что вы передаете идентификатор ресурса спецификации шаблона. Вы используете те же команды развертывания и при необходимости передайте значения параметров для спецификации шаблона.

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о развертывании шаблона спецификации в качестве связанного шаблона см. в разделе [учебник. Развертывание шаблона спецификации в качестве связанного шаблона](template-specs-deploy-linked-template.md).
