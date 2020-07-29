---
title: Развертывание ресурсов в группе управления
description: Описывает развертывание ресурсов в области группы управления в шаблоне Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: a17387aef4d35c042d1fe0b02f1c6fd447e4a918
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321808"
---
# <a name="create-resources-at-the-management-group-level"></a>Создание ресурсов на уровне группы управления

По мере развития вашей организации можно развернуть шаблон Azure Resource Manager (шаблон ARM) для создания ресурсов на уровне группы управления. Например, может потребоваться определить и назначить [политики](../../governance/policy/overview.md) или [элементы управления доступом на основе ролей](../../role-based-access-control/overview.md) для группы управления. С помощью шаблонов уровня группы управления можно декларативно применять политики и назначать роли на уровне группы управления.

## <a name="supported-resources"></a>Поддерживаемые ресурсы

Не все типы ресурсов можно развернуть на уровне группы управления. В этом разделе перечислены поддерживаемые типы ресурсов.

Для чертежей Azure используйте:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints);
* [блуепринтассигнментс](/azure/templates/microsoft.blueprint/blueprintassignments)
* [операционных](/azure/templates/microsoft.blueprint/blueprints/versions)

Для политик Azure используйте:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments);
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions);
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions);
* [remediations](/azure/templates/microsoft.policyinsights/remediations);

Для управления доступом на основе ролей используйте:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments);
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions).

Для вложенных шаблонов, которые развертываются в подписках или группах ресурсов, используйте:

* [размещения](/azure/templates/microsoft.resources/deployments)

Для управления ресурсами используйте:

* [теги](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>Схема

Схема, используемая для развертываний группы управления, отличается от схемы развертываний группы ресурсов.

Для шаблонов используйте:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Схема для файла параметров одинакова для всех областей развертывания. Для файлов параметров используйте:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Команды развертывания

Команды для развертываний группы управления отличаются от команд для развертываний групп ресурсов.

Для Azure CLI используйте команду [AZ Deployment mg Create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Для Azure PowerShell используйте [New-азманажементграупдеплоймент](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Для REST API используйте [развертывания — создание в области группы управления](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Расположение и имя развертывания

Для развертываний на уровне группы управления необходимо указать расположение для развертывания. Расположение развертывания отделено от расположения развертываемых ресурсов. В расположении развертывания указывается место хранения данных развертывания.

Можно указать имя развертывания или использовать имя развертывания по умолчанию. Имя по умолчанию — это имя файла шаблона. Например, развернув шаблон с именем **azuredeploy.json** создается имя развертывания по умолчанию **azuredeploy**.

Для каждого имени развертывания расположение остается неизменным. Нельзя создать развертывание в одном расположении, если в другом уже есть развертывание с таким же именем. Если появится код ошибки `InvalidDeploymentLocation`, используйте другое имя или то же расположение, что и для предыдущего развертывания с этим именем.

## <a name="deployment-scopes"></a>Области развертывания

При развертывании в группе управления можно выбрать целевую группу управления, указанную в команде развертывания или в других группах управления в клиенте. Вы также можете ориентироваться на подписки или группы ресурсов в группе управления. Пользователь, развертывающий шаблон, должен иметь доступ к указанной области.

Ресурсы, определенные в разделе ресурсов шаблона, применяются к группе управления из команды развертывания.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        management-group-level-resources
    ],
    "outputs": {}
}
```

Чтобы выбрать другую группу управления, добавьте вложенное развертывание и укажите `scope` свойство.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string"
        }
    },
    "variables": {
        "mgId": "[concat('Microsoft.Management/managementGroups/', parameters('mgName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2019-10-01",
            "name": "nestedDeployment",
            "scope": "[variables('mgId')]",
            "location": "eastus",
            "properties": {
                "mode": "Incremental",
                "template": {
                    nested-template
                }
            }
        }
    ],
    "outputs": {}
}
```

Чтобы назначить подписку в группе управления, используйте вложенное развертывание и `subscriptionId` свойство. Чтобы назначить группу ресурсов в рамках этой подписки, добавьте еще одно вложенное развертывание и `resourceGroup` свойство.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "westus2",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedRG",
              "resourceGroup": "rg2",
              "properties": {
                "mode": "Incremental",
                "template": {
                  nested-template
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="use-template-functions"></a>Использование функций шаблонов

При развертывании группы управления при использовании функций шаблона необходимо учитывать некоторые важные моменты.

* Функция [resourceGroup()](template-functions-resource.md#resourcegroup)**не** поддерживается.
* Функция [subscription()](template-functions-resource.md#subscription) **не** поддерживается.
* Функции [reference()](template-functions-resource.md#reference) и [list()](template-functions-resource.md#list) поддерживаются.
* Функция [resourceId()](template-functions-resource.md#resourceid) поддерживается. Используйте его, чтобы получить идентификатор ресурса для ресурсов, используемых при развертывании на уровне группы управления. Не следует указывать значение параметра группы ресурсов.

  Например, чтобы получить идентификатор ресурса для определения политики, используйте:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Возвращенный идентификатор ресурса имеет следующий формат:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="azure-policy"></a>Политика Azure

### <a name="define-policy"></a>Определение политики

В следующем примере показано, как [определить](../../governance/policy/concepts/definition-structure.md) политику на уровне группы управления.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
    }
  ]
}
```

### <a name="assign-policy"></a>Назначение политики

В следующем примере существующее определение политики назначается группе управления. Предоставьте параметры в качестве объекта, если политика принимает их. Используйте пустой объект по умолчанию, если политика не принимает параметры.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Развертывание в подписке и группе ресурсов

Из развертывания на уровне группы управления можно ориентироваться на подписку в группе управления. В следующем примере создается группа ресурсов в рамках подписки и выполняется развертывание учетной записи хранения в этой группе ресурсов.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о назначении ролей см. в статье об [управлении доступом к ресурсам Azure с помощью RBAC и шаблонов Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Пример развертывания параметров рабочей области для центра безопасности Azure см. в разделе о [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Можно также развернуть шаблоны на уровне [подписки](deploy-to-subscription.md) и на [уровне клиента](deploy-to-tenant.md).
