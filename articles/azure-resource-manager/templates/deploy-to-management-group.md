---
title: Развертывание ресурсов в группе управления
description: Описывает, как развертывать ресурсы в области группы управления в шаблоне Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 863d1330412fa238b820eb0f1f05351fc723de6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460319"
---
# <a name="create-resources-at-the-management-group-level"></a>Создание ресурсов на уровне управленческой группы

По мере взросления организации может потребоваться определить и назначить [политики](../../governance/policy/overview.md) или [элементы управления доступом на основе ролей](../../role-based-access-control/overview.md) для группы управления. С помощью шаблонов уровня уровень управления можно декларативно применять политики и назначать роли на уровне группы управления.

## <a name="supported-resources"></a>Поддерживаемые ресурсы

Вы можете развернуть следующие типы ресурсов на уровне группы управления:

* развертывание - для вложенных шаблонов, которые развертываются в подписях или [группах](/azure/templates/microsoft.resources/deployments) ресурсов.
* [политикаНазначения](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [ролевыеназначения](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>схема

Схема, используемая для развертывания групп управления, отличается от схемы развертывания группы ресурсов.

Для шаблонов используйте:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Схема для файла параметров одинакова для всех областей развертывания. Для параметров используйте:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Команды развертывания

Команды для развертывания групп управления отличаются от команд для развертывания групп ресурсов.

Для Azure CLI используйте [az развертывание мг создать:](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Для Azure PowerShell используйте [New-AzManagementGroupDeployment.](/powershell/module/az.resources/new-azmanagementgroupdeployment)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Для REST API используйте [развертывание - Создайте область управления.](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)

## <a name="deployment-location-and-name"></a>Местоположение и имя развертывания

Для развертывания группы управления необходимо учесть место развертывания. Расположение развертывания отделено от местоположения развертываемых ресурсов. Местоположение развертывания определяет, где хранить данные развертывания.

Имя развертывания можно усваить или использовать имя развертывания по умолчанию. Имя по умолчанию — это имя файла шаблона. Например, развернув шаблон с именем **azuredeploy.json** создается имя развертывания по умолчанию **azuredeploy**.

Для каждого имени развертывания местоположение неизменяемо. Развертывание не может быть создано в одном месте, когда есть существующее развертывание с тем же именем в другом месте. Если появится код ошибки `InvalidDeploymentLocation`, используйте другое имя или то же расположение, что и для предыдущего развертывания с этим именем.

## <a name="use-template-functions"></a>Использование функций шаблонов

При развертывании групп управления при использовании функций шаблонов существуют некоторые важные соображения:

* Функция [resourceGroup()](template-functions-resource.md#resourcegroup)**не** поддерживается.
* Функция [подписки ()](template-functions-resource.md#subscription) **не** поддерживается.
* Функции [reference()](template-functions-resource.md#reference) и [list()](template-functions-resource.md#list) поддерживаются.
* Функция [resourceId()](template-functions-resource.md#resourceid) поддерживается. Используйте его для получения идентификатора ресурсов для ресурсов, используемых при развертывании на уровне группы управления. Не предоставляйте значение для параметра группы ресурсов.

  Например, чтобы получить идентификатор ресурсов для определения политики, используйте:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Идентификатор возвращенного ресурса имеет следующий формат:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>Создайте политики.

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

Следующий пример присваивает существующую определение политики группе управления. Предоставьте параметры в качестве объекта, если политика принимает их. Используйте пустой объект по умолчанию, если политика не принимает параметры.

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

## <a name="template-sample"></a>Образец шаблона

* [Создайте группу ресурсов, политику и назначение политики.](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json)

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать о назначении ролей, [см. Управление доступом к ресурсам Azure с помощью шаблонов RBAC и azure Resource Manager.](../../role-based-access-control/role-assignments-template.md)
* Пример развертывания параметров рабочей области для центра безопасности Azure см. в разделе о [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Вы также можете развертывать шаблоны на [уровне подписки](deploy-to-subscription.md) и [уровне клиента.](deploy-to-tenant.md)
