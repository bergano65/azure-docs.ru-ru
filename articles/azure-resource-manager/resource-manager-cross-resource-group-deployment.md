---
title: Развертывание ресурсов межподписок & группы ресурсов
description: Сведения о развертывании ресурсов в нескольких подписках и группах ресурсов Azure.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0754895215384f76b1cb44224f3ba06c80181827
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978770"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Развертывание ресурсов Azure в нескольких подписках или группах ресурсов

Как правило, все ресурсы в шаблоне развертываются в отдельную [группу ресурсов](resource-group-overview.md). Тем не менее возможны ситуации, когда необходимо развернуть набор ресурсов одновременно, но при этом разместить их в отдельных подписках или группах ресурсов. Например, вы захотите развернуть резервную копию виртуальной машины для Azure Site Recovery в отдельную группу ресурсов или расположение. Диспетчер ресурсов позволяет использовать вложенные шаблоны для более чем одной подписки и группы ресурсов.

> [!NOTE]
> Развертывание можно выполнять только в пять групп ресурсов в рамках одного развертывания. Как правило, это ограничение означает, что можно выполнить развертывание в одной группе ресурсов, указанной для родительского шаблона, и максимум в четырех группах ресурсов во вложенных или связанных развертываниях. Но если родительский шаблон содержит только вложенные или связанные шаблоны и сам по себе не развертывает ресурсы, во вложенные или связанные развертывания можно включить до пяти групп ресурсов.

## <a name="specify-subscription-and-resource-group"></a>Укажите подписку и группу ресурсов

Чтобы выбрать другую группу ресурсов или подписку, используйте [вложенный или связанный шаблон](resource-group-linked-templates.md). Тип ресурса `Microsoft.Resources/deployments` предоставляет параметры для `subscriptionId` и `resourceGroup`, которые позволяют указать подписку и группу ресурсов для вложенного развертывания. Если вы не укажете идентификатор подписки или группу ресурсов, используется подписка и группа ресурсов из родительского шаблона. Все группы ресурсов необходимо создать до выполнения развертывания.

Учетная запись, которая используется для развертывания шаблона, должна иметь разрешения на развертывание с указанным идентификатором подписки. Если указанная подписка существует в другом клиенте Azure Active Directory, необходимо [добавить гостевых пользователей из другого каталога](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Чтобы указать другую группу ресурсов и подписку, используйте следующий код:

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

Если группы ресурсов находятся в одной подписке, значение **subscriptionId** можно удалить.

В следующем примере развертываются две учетные записи хранения. Первая учетная запись хранения развертывается в группе ресурсов, указанной во время развертывания. Вторая учетная запись хранения развертывается в группе ресурсов, указанной в параметрах `secondResourceGroup` и `secondSubscriptionID`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        }
    ]
}
```

Если присвоить `resourceGroup` имя несуществующей группы ресурсов, развертывание завершится ошибкой.

Чтобы протестировать предыдущий шаблон и просмотреть результаты, используйте PowerShell или Azure CLI.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы развернуть две учетные записи хранения в двух группах ресурсов в **одной подписке**, используйте:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Чтобы развернуть две учетные записи хранения в **двух подписках**, используйте:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы развернуть две учетные записи хранения в двух группах ресурсов в **одной подписке**, используйте:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Чтобы развернуть две учетные записи хранения в **двух подписках**, используйте:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Использование функций

Функции [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) и [Subscription ()](resource-group-template-functions-resource.md#subscription) разрешаются по-разному в зависимости от способа указания шаблона. При связывании с внешним шаблоном функции всегда разрешаются в область этого шаблона. При вложении шаблона в родительский шаблон используйте свойство `expressionEvaluationOptions`, чтобы указать, будут ли функции разрешаться в группу ресурсов и подписку для родительского шаблона или вложенного шаблона. Задайте для свойства значение `inner`, чтобы разрешить область вложенного шаблона. Задайте для свойства значение `outer`, чтобы разрешить область действия родительского шаблона.

В следующей таблице показано, разрешены ли функции в родительскую или внедренную группу ресурсов и подписку.

| Тип шаблона | Область действия | Разрешение |
| ------------- | ----- | ---------- |
| вложенные        | внешний (по умолчанию) | Родительская группа ресурсов |
| вложенные        | Внутреннее | Подкатегория "подгруппа ресурсов" |
| связанные        | Н/Д   | Подкатегория "подгруппа ресурсов" |

В следующем [примере шаблона]((https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)) показано следующее:

* вложенный шаблон с областью (внешней) по умолчанию
* вложенный шаблон с внутренней областью
* связанный шаблон

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "defaultScopeTemplate",
            "apiVersion": "2017-05-10",
            "resourceGroup": "inlineGroup",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                    ],
                    "outputs": {
                        "resourceGroupOutput": {
                            "type": "string",
                            "value": "[resourceGroup().name]"
                        }
                    }
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "name": "innerScopeTemplate",
            "apiVersion": "2017-05-10",
            "resourceGroup": "inlineGroup",
            "properties": {
                "expressionEvaluationOptions": {
                    "scope": "inner"
                },
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                    ],
                    "outputs": {
                        "resourceGroupOutput": {
                            "type": "string",
                            "value": "[resourceGroup().name]"
                        }
                    }
                },
                "parameters": {}
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "linkedGroup",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/resourceGroupName.json"
                },
                "parameters": {}
            }
        }
    ],
    "outputs": {
        "parentRG": {
            "type": "string",
            "value": "[concat('Parent resource group is ', resourceGroup().name)]"
        },
        "defaultScopeRG": {
            "type": "string",
            "value": "[concat('Default scope resource group is ', reference('defaultScopeTemplate').outputs.resourceGroupOutput.value)]"
        },
        "innerScopeRG": {
            "type": "string",
            "value": "[concat('Inner scope resource group is ', reference('innerScopeTemplate').outputs.resourceGroupOutput.value)]"
        },
        "linkedRG": {
            "type": "string",
            "value": "[concat('Linked resource group is ', reference('linkedTemplate').outputs.resourceGroupOutput.value)]"
        }
    }
}
```

Чтобы протестировать предыдущий шаблон и просмотреть результаты, используйте PowerShell или Azure CLI.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Выходные данные из предыдущего примера:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

Выходные данные из предыдущего примера:

```azurecli
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об определении параметров в шаблоне см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-powershell-sas-token.md).
