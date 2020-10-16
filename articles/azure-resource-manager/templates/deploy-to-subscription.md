---
title: Развертывание ресурсов в подписке
description: В этой статье описывается создание группы ресурсов в шаблоне Azure Resource Manager. Здесь также показано, как развернуть ресурсы в области подписки Azure.
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 0673ea5260c7312395acde8a62b5d457657b9793
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91729123"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Создание групп ресурсов и ресурсов на уровне подписки

Чтобы упростить управление ресурсами, можно использовать шаблон Azure Resource Manager (шаблон ARM) для развертывания ресурсов на уровне подписки Azure. Например, можно развернуть [политики](../../governance/policy/overview.md) и [Управление доступом на основе ролей Azure (Azure RBAC)](../../role-based-access-control/overview.md) в подписке, которая применяет их в рамках подписки. Вы также можете создавать группы ресурсов в подписке и развертывать ресурсы в группах ресурсов в подписке.

> [!NOTE]
> На уровне подписки можно развернуть до 800 различных групп ресурсов.

Чтобы развернуть шаблоны на уровне подписки, используйте Azure CLI, PowerShell, REST API или портал.

## <a name="supported-resources"></a>Поддерживаемые ресурсы

Не все типы ресурсов можно развернуть на уровне подписки. В этом разделе перечислены поддерживаемые типы ресурсов.

Для чертежей Azure используйте:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints);
* [блуепринтассигнментс](/azure/templates/microsoft.blueprint/blueprintassignments)
* [версии (проекты)](/azure/templates/microsoft.blueprint/blueprints/versions)

Для политик Azure используйте:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments);
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions);
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions);
* [remediations](/azure/templates/microsoft.policyinsights/remediations);

Для управления доступом на основе ролей Azure (Azure RBAC) используйте:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments);
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions).

Для вложенных шаблонов, которые развертываются в группах ресурсов, используйте:

* [размещения](/azure/templates/microsoft.resources/deployments)

Для создания новых групп ресурсов используйте:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Для управления подпиской используйте:

* [Конфигурации Advisor](/azure/templates/microsoft.advisor/configurations)
* [budgets](/azure/templates/microsoft.consumption/budgets);
* [Изменить профиль анализа](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes);
* [теги](/azure/templates/microsoft.resources/tags)

К другим поддерживаемым типам относятся:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments);
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions);
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns);

## <a name="schema"></a>схема

Схема, используемая для развертываний на уровне подписки, отличается от схемы развертываний группы ресурсов.

Для шаблонов используйте:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Схема для файла параметров одинакова для всех областей развертывания. Для файлов параметров используйте:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-scopes"></a>Области развертывания

При развертывании в подписку можно ориентироваться на одну подписку и любые группы ресурсов в рамках подписки. Нельзя выполнить развертывание в подписке, которая отличается от целевой подписки. Пользователь, развертывающий шаблон, должен иметь доступ к указанной области.

Ресурсы, определенные в разделе ресурсов шаблона, применяются к подписке.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Чтобы назначить группу ресурсов в подписке, добавьте вложенное развертывание и включите `resourceGroup` свойство. В следующем примере вложенное развертывание предназначено для группы ресурсов с именем `rg2` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

В этой статье можно найти шаблоны, демонстрирующие развертывание ресурсов в различных областях. Сведения о шаблоне, который создает группу ресурсов и развертывает в ней учетную запись хранения, см. в разделе [Создание группы ресурсов и ресурсов](#create-resource-group-and-resources). Для шаблона, который создает группу ресурсов, применяет к ней блокировку и назначает роль для группы ресурсов, см. раздел [Управление доступом](#access-control).

## <a name="deployment-commands"></a>Команды развертывания

Команды, используемые для развертываний на уровне подписки, отличаются от команд для развертываний группы ресурсов.

Для Azure CLI используйте [az deployment sub create](/cli/azure/deployment/sub#az-deployment-sub-create). В следующем примере выполняется развертывание шаблона для создания группы ресурсов.

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

В качестве команды развертывания в PowerShell используйте [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) или **New-AzSubscriptionDeployment**. В следующем примере выполняется развертывание шаблона для создания группы ресурсов.

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

Для REST API используйте инструкции в статье [Развертывания — создание в области подписки](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Расположение и имя развертывания

Для развертываний на уровне подписки необходимо указать расположение для развертывания. Расположение развертывания отделено от расположения развертываемых ресурсов. В расположении развертывания указывается место хранения данных развертывания.

Можно указать имя развертывания или использовать имя развертывания по умолчанию. Имя по умолчанию — это имя файла шаблона. Например, развернув шаблон с именем **azuredeploy.json** создается имя развертывания по умолчанию **azuredeploy**.

Для каждого имени развертывания расположение остается неизменным. Нельзя создать развертывание в одном расположении, если в другом уже есть развертывание с таким же именем. Если появится код ошибки `InvalidDeploymentLocation`, используйте другое имя или то же расположение, что и для предыдущего развертывания с этим именем.

## <a name="use-template-functions"></a>Использование функций шаблонов

Важные рекомендации при использовании функций шаблонов для развертываний на уровне подписки:

* Функция [resourceGroup()](template-functions-resource.md#resourcegroup)**не** поддерживается.
* Функции [reference()](template-functions-resource.md#reference) и [list()](template-functions-resource.md#list) поддерживаются.
* Не используйте [resourceId ()](template-functions-resource.md#resourceid) , чтобы получить идентификатор ресурса для ресурсов, развернутых на уровне подписки. Вместо этого используйте функцию [субскриптионресаурцеид ()](template-functions-resource.md#subscriptionresourceid) .

  Например, чтобы получить идентификатор ресурса для определения политики, развернутой в подписке, используйте:

  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```

  Возвращенный идентификатор ресурса имеет следующий формат:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="resource-groups"></a>Группы ресурсов

### <a name="create-resource-groups"></a>Создание группы ресурсов

Чтобы создать группу ресурсов в шаблоне ARM, определите ресурс [Microsoft. Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) с именем и расположением группы ресурсов.

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
      "apiVersion": "2020-06-01",
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
      "apiVersion": "2020-06-01",
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

Дополнительные сведения см. в статьях [Развертывание нескольких экземпляров ресурса в шаблонах Azure Resource Manager](./copy-resources.md) и [Руководство. Создание нескольких экземпляров ресурса с помощью шаблонов Resource Manager](./template-tutorial-create-multiple-instances.md).

### <a name="create-resource-group-and-resources"></a>Создание группы ресурсов и ресурсов

Чтобы создать группу ресурсов и развернуть в нее ресурсы, используйте вложенный шаблон. Вложенный шаблон определяет ресурсы для развертывания в группе ресурсов. Установите вложенный шаблон как зависимый от группы ресурсов, чтобы группа существовала до развертывания ресурсов. Можно выполнить развертывание до 800 групп ресурсов.

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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
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

В следующем примере подписке присваивается имеющееся определение политики. Предоставьте параметры в качестве объекта, если определение политики принимает их. Используйте пустой объект по умолчанию, если определение политики не принимает параметры.

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

### <a name="create-and-assign-policy-definitions"></a>Создание и назначение определения политики

Вы можете [определить](../../governance/policy/concepts/definition-structure.md) и назначить определение политики в том же шаблоне.

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
        "policyDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

Чтобы создать определение политики и назначить ее в подписке, используйте следующую команду CLI.

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

### <a name="create-blueprint-definition"></a>Создание определения схемы

Можно [создать](../../governance/blueprints/tutorials/create-from-sample.md) определение схемы из шаблона.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Чтобы создать определение схемы в подписке, используйте следующую команду CLI.

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Развернуть этот шаблон с помощью PowerShell можно так:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Управление доступом

Дополнительные сведения о назначении ролей см. в статье [Добавление назначений ролей Azure с помощью шаблонов Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).

Следующий пример создает группу ресурсов, применяет к ней блокировку и назначает роль участнику.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Дальнейшие шаги

* Пример развертывания параметров рабочей области для центра безопасности Azure см. в разделе о [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Примеры шаблонов можно найти на [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments).
* Кроме того, можно развернуть шаблоны на [уровне группы управления](deploy-to-management-group.md) и на [уровне клиента](deploy-to-tenant.md).
