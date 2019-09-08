---
title: Создание группы ресурсов и ресурсов в подписке — шаблон Azure Resource Manager
description: В этой статье описывается создание группы ресурсов в шаблоне Azure Resource Manager. Здесь также показано, как развернуть ресурсы в области подписки Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: tomfitz
ms.openlocfilehash: 37f2b04a62d94cce42b095540380460c38bc5b79
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772941"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Создание групп ресурсов и ресурсов на уровне подписки

Как правило, вы развертываете ресурсы Azure в группу ресурсов в подписке Azure. Но можете также создавать группы ресурсов Azure и создавать ресурсы Azure на уровне подписки. Чтобы развернуть шаблоны на уровне подписки, используйте Azure CLI и Azure PowerShell. Портал Azure не поддерживает развертывания на уровне подписки.

Чтобы создать группу ресурсов в шаблоне Azure Resource Manager, определите для ресурса [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) имя и расположение. Вы можете создать группу ресурсов и развернуть ресурсы в нее в одном шаблоне. Ресурсы, которые можно развернуть на уровне подписки: [Политики](../governance/policy/overview.md) и [Управление доступом на основе ролей](../role-based-access-control/overview.md).

## <a name="deployment-considerations"></a>Рекомендации по развертыванию

Развертывание уровня подписки отличается от развертывания группы ресурсов следующими аспектами.

### <a name="schema-and-commands"></a>Схема и команды

Схема и команды, используемые для развертываний на уровне подписки, отличаются от развертываний в группу ресурсов. 

Для схемы используйте `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

В качестве команды развертывания Azure CLI используйте [az deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). Например, следующая команда CLI развертывает шаблон для создания группы ресурсов.

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

В качестве команды развертывания PowerShell используйте [New-AzureRmDeployment](/powershell/module/az.resources/new-azdeployment). Например, следующая команда PowerShell развертывает шаблон для создания группы ресурсов.

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>Имя и расположение для развертывания

При развертывании в подписке, необходимо указать расположение для развертывания. Можно также указать имя для развертывания. Если не указать имя для развертывания, имя шаблона будет использоваться как имя развертывания. Например, развернув шаблон с именем **azuredeploy.json** создается имя развертывания по умолчанию **azuredeploy**.

Расположение развертывания уровня подписки является неизменяемым. Не возможно создать развертывание в одном расположении, если в другом расположении уже существует развертывание с таким же именем. Если появится код ошибки `InvalidDeploymentLocation`, используйте другое имя или то же расположение, что и для предыдущего развертывания с этим именем.

### <a name="use-template-functions"></a>Использование функций шаблонов

Важные рекомендации при использовании функций шаблонов для развертываний на уровне подписки:

* Функция [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) **не** поддерживается.
* Функция [resourceId()](resource-group-template-functions-resource.md#resourceid) поддерживается. Используйте ее для получения идентификатора ресурса для ресурсов, которые используются в развертываниях уровня подписки. Например получите идентификатор ресурса для определения политики с помощью `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* Функции [reference()](resource-group-template-functions-resource.md#reference) и [list()](resource-group-template-functions-resource.md#list) поддерживаются.

## <a name="create-resource-groups"></a>Создание группы ресурсов

В следующем примере создается пустая группа ресурсов.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Схему шаблона можно найти [здесь](/azure/templates/microsoft.resources/allversions). Аналогичные шаблоны можно найти в [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).

## <a name="create-multiple-resource-groups"></a>Создание нескольких групп ресурсов

Используйте [элемент copy](resource-group-create-multiple.md) с группами ресурсов, чтобы создать несколько групп ресурсов. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Дополнительные сведения см. в статьях [Развертывание нескольких экземпляров ресурса или свойства в шаблонах Azure Resource Manager](./resource-group-create-multiple.md) и [Руководство. Создание нескольких экземпляров ресурса с помощью шаблонов Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="create-resource-group-and-deploy-resources"></a>Создание группы ресурсов и развертывание ресурсов

Чтобы создать группу ресурсов и развернуть в нее ресурсы, используйте вложенный шаблон. Вложенный шаблон определяет ресурсы для развертывания в группе ресурсов. Установите вложенный шаблон как зависимый от группы ресурсов, чтобы группа существовала до развертывания ресурсов.

В следующем примере создается группа ресурсов и в нее развертывается учетная запись хранения.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
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
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="create-policies"></a>Создать политики

### <a name="assign-policy"></a>Назначить политику

В следующем примере подписке присваивается имеющееся определение политики. Предоставьте параметры в качестве объекта, если политика принимает их. Используйте пустой объект по умолчанию, если политика не принимает параметры.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```

Развернуть этот шаблон с помощью Azure CLI можно так:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Развернуть этот шаблон с помощью PowerShell можно так:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Определение и назначение политики

Вы можете [определить](../governance/policy/concepts/definition-structure.md) и назначить политику в том же шаблоне.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2018-05-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

Чтобы создать определение политики в подписке и применить его к ней, используйте следующую команду CLI.

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Развернуть этот шаблон с помощью PowerShell можно так:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о назначении ролей см. в статье [Управление доступом к ресурсам Azure с помощью RBAC и шаблонов Azure Resource Manager](../role-based-access-control/role-assignments-template.md).
* Пример развертывания параметров рабочей области для центра безопасности Azure см. в разделе о [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Сведения о создании шаблонов диспетчера ресурсов Azure см. в статье о [создании шаблонов](resource-group-authoring-templates.md). 
* Список доступных в шаблоне функций см. в статье о [функциях шаблонов](resource-group-template-functions.md).
