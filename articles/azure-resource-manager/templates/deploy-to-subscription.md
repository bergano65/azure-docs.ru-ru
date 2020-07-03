---
title: Развертывание ресурсов в подписке
description: В этой статье описывается создание группы ресурсов в шаблоне Azure Resource Manager. Здесь также показано, как развернуть ресурсы в области подписки Azure.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: a48bc2fd4efb383b42fd0889df079c9a6f700dda
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929066"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Создание групп ресурсов и ресурсов на уровне подписки

Чтобы упростить управление ресурсами в подписке Azure, можно определить и назначить [политики](../../governance/policy/overview.md) или [элементы управления доступом на основе ролей](../../role-based-access-control/overview.md) в рамках подписки. С помощью шаблонов уровня подписки декларативно применяются политики и назначаются роли в подписке. Вы также можете создавать группы ресурсов и развертывать ресурсы.

Чтобы развернуть шаблоны на уровне подписки, используйте Azure CLI, PowerShell или REST API. Портал Azure не поддерживает развертывания на уровне подписки.

## <a name="supported-resources"></a>Поддерживаемые ресурсы

На уровне подписки можно развернуть следующие типы ресурсов:

* [проекты](/azure/templates/microsoft.blueprint/blueprints)
* [увеличен](/azure/templates/microsoft.consumption/budgets)
* [развертывания](/azure/templates/microsoft.resources/deployments) — для вложенных шаблонов, которые развертываются в группах ресурсов.
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [пираснс](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)
* [полициассигнментс](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [исправления](/azure/templates/microsoft.policyinsights/2019-07-01/remediations)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)
* [скопеассигнментс](/azure/templates/microsoft.managednetwork/scopeassignments)
* [суппортплантипес](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Тэги](/azure/templates/microsoft.resources/tags)
* [воркспацесеттингс](/azure/templates/microsoft.security/workspacesettings)

### <a name="schema"></a>схема

Схема, используемая для развертываний на уровне подписки, отличается от схемы развертываний группы ресурсов.

Для шаблонов используйте:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Схема для файла параметров одинакова для всех областей развертывания. Для файлов параметров используйте:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Команды развертывания

Команды для развертываний на уровне подписки отличаются от команд для развертываний групп ресурсов.

Для Azure CLI используйте команду [AZ Deployment подсоздание](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create). В следующем примере выполняется развертывание шаблона для создания группы ресурсов.

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Для команды развертывания PowerShell используйте [New-аздеплоймент](/powershell/module/az.resources/new-azdeployment) или **New-азсубскриптиондеплоймент**. В следующем примере выполняется развертывание шаблона для создания группы ресурсов.

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

Для REST API используйте [развертывания — создание в области подписки](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Расположение и имя развертывания

Для развертываний на уровне подписки необходимо указать расположение для развертывания. Расположение развертывания отделено от расположения развертываемых ресурсов. В расположении развертывания указывается место хранения данных развертывания.

Можно указать имя развертывания или использовать имя развертывания по умолчанию. Имя по умолчанию — это имя файла шаблона. Например, развернув шаблон с именем **azuredeploy.json** создается имя развертывания по умолчанию **azuredeploy**.

Для каждого имени развертывания расположение является неизменяемым. Нельзя создать развертывание в одном месте, если существует развертывание с тем же именем в другом расположении. Если появится код ошибки `InvalidDeploymentLocation`, используйте другое имя или то же расположение, что и для предыдущего развертывания с этим именем.

## <a name="use-template-functions"></a>Использование функций шаблонов

Важные рекомендации при использовании функций шаблонов для развертываний на уровне подписки:

* Функция [resourceGroup()](template-functions-resource.md#resourcegroup)**не** поддерживается.
* Функции [reference()](template-functions-resource.md#reference) и [list()](template-functions-resource.md#list) поддерживаются.
* Используйте функцию [субскриптионресаурцеид ()](template-functions-resource.md#subscriptionresourceid) , чтобы получить идентификатор ресурса для ресурсов, развернутых на уровне подписки.

  Например, чтобы получить идентификатор ресурса для определения политики, используйте:

  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```

  Возвращенный идентификатор ресурса имеет следующий формат:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-resource-groups"></a>Создание группы ресурсов

Чтобы создать группу ресурсов в шаблоне Azure Resource Manager, определите имя и расположение ресурса [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) для группы ресурсов. Вы можете создать группу ресурсов и развернуть ресурсы в нее в одном шаблоне.

В следующем примере создается пустая группа ресурсов.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Используйте [элемент copy](copy-resources.md) с группами ресурсов, чтобы создать несколько групп ресурсов.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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

Дополнительные сведения о итерации ресурсов см. [в разделе Развертывание нескольких экземпляров ресурса в Azure Resource Manager шаблонах](./copy-resources.md)и [учебник. Создание нескольких экземпляров ресурсов с помощью шаблонов диспетчер ресурсов](./template-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Группа ресурсов и ресурсы

Чтобы создать группу ресурсов и развернуть в нее ресурсы, используйте вложенный шаблон. Вложенный шаблон определяет ресурсы для развертывания в группе ресурсов. Установите вложенный шаблон как зависимый от группы ресурсов, чтобы группа существовала до развертывания ресурсов.

В следующем примере создается группа ресурсов и в нее развертывается учетная запись хранения.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
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

## <a name="azure-policy"></a>Политика Azure

### <a name="assign-policy-definition"></a>Назначение определения политики

В следующем примере подписке присваивается имеющееся определение политики. Если определение политики принимает параметры, укажите их в качестве объекта. Если определение политики не принимает параметры, используйте пустой объект по умолчанию.

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
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
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
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Развернуть этот шаблон с помощью PowerShell можно так:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>Создание и назначение определений политик

Определение политики можно [определить](../../governance/policy/concepts/definition-structure.md) и назначить в том же шаблоне.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
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
      "apiVersion": "2018-05-01",
      "name": "location-lock",
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

Чтобы создать определение политики в подписке и назначить его подписке, используйте следующую команду интерфейса командной строки:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Развернуть этот шаблон с помощью PowerShell можно так:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>Создать определение схемы

Определение схемы можно [создать](../../governance/blueprints/tutorials/create-from-sample.md) на основе шаблона.

:::code language="json" source="~/quickstart-templates/subscription-level-deployments/blueprints-new-blueprint/azuredeploy.json":::

Чтобы создать определение схемы в подписке, используйте следующую команду интерфейса командной строки:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-level-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Развернуть этот шаблон с помощью PowerShell можно так:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-level-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="template-samples"></a>Примеры шаблона

* [Создайте группу ресурсов, заблокируйте ее и предоставьте ей разрешения](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).
* [Создайте группу ресурсов, политику и назначение политики](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о назначении ролей см. в статье [Управление доступом к ресурсам Azure с помощью RBAC и шаблонов Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Пример развертывания параметров рабочей области для центра безопасности Azure см. в разделе о [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Примеры шаблонов можно найти на сайте [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* Вы также можете развернуть шаблоны на уровне [группы управления](deploy-to-management-group.md) и на [уровне клиента](deploy-to-tenant.md).
