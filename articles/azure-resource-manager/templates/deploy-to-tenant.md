---
title: Развертывание ресурсов для арендатора
description: Описывает, как развертывать ресурсы в области клиента в шаблоне Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460268"
---
# <a name="create-resources-at-the-tenant-level"></a>Создание ресурсов на уровне арендатора

По мере взросления организации может потребоваться определить и назначить [политики](../../governance/policy/overview.md) или [элементы управления доступом на основе ролей](../../role-based-access-control/overview.md) в вашем арендаторе Azure AD. С помощью шаблонов уровня клиента можно декларативно применять политики и назначать роли на глобальном уровне.

## <a name="supported-resources"></a>Поддерживаемые ресурсы

Можно развернуть следующие типы ресурсов на уровне арендатора:

* [развертывание](/azure/templates/microsoft.resources/deployments) - для вложенных шаблонов, которые развертываются в группах управления или подписках.
* [политикаНазначения](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [ролевыеназначения](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>схема

Схема, используемая для развертывания клиентов, отличается от схемы для развертывания групп ресурсов.

Для шаблонов используйте:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Схема для файла параметров одинакова для всех областей развертывания. Для параметров используйте:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Необходимый доступ

Основное развертывание шаблона должно иметь разрешения на создание ресурсов в области арендатора. Директор должен иметь разрешение на выполнение`Microsoft.Resources/deployments/*`действий развертывания () и создание ресурсов, определенных в шаблоне. Например, для создания группы управления основной должен иметь разрешение на участие в проекте клиента. Для создания назначений ролей директор должен иметь разрешение владельца.

Глобальный администратор для активного каталога Azure автоматически не имеет разрешения на присвоение ролей. Для включения развертывания шаблонов в области клиента Глобальный администратор должен сделать следующие шаги:

1. Увелиите доступ к учетной записи, чтобы Глобальный администратор мог назначить роли. Для получения дополнительной [Elevate access to manage all Azure subscriptions and management Groups](../../role-based-access-control/elevate-access-global-admin.md)информации см.

1. Назначить владельца или вкладчика в основной, который должен развернуть шаблоны.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Теперь у основного принципала есть необходимые разрешения на развертывание шаблона.

## <a name="deployment-commands"></a>Команды развертывания

Команды для развертывания клиентов отличаются от команд для развертывания групп ресурсов.

Для Azure CLI используйте [создание клиента развертывания az:](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Для Azure PowerShell используйте [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Для REST API используйте [развертывание - Создайте или обновите область в области арендаторов.](/rest/api/resources/deployments/createorupdateattenantscope)

## <a name="deployment-location-and-name"></a>Местоположение и имя развертывания

Для развертывания уровня клиента необходимо учесть место развертывания. Расположение развертывания отделено от местоположения развертываемых ресурсов. Местоположение развертывания определяет, где хранить данные развертывания.

Имя развертывания можно усваить или использовать имя развертывания по умолчанию. Имя по умолчанию — это имя файла шаблона. Например, развернув шаблон с именем **azuredeploy.json** создается имя развертывания по умолчанию **azuredeploy**.

Для каждого имени развертывания местоположение неизменяемо. Развертывание не может быть создано в одном месте, когда есть существующее развертывание с тем же именем в другом месте. Если появится код ошибки `InvalidDeploymentLocation`, используйте другое имя или то же расположение, что и для предыдущего развертывания с этим именем.

## <a name="use-template-functions"></a>Использование функций шаблонов

При развертывании клиентов при использовании функций шаблона существуют некоторые важные соображения:

* Функция [resourceGroup()](template-functions-resource.md#resourcegroup)**не** поддерживается.
* Функция [подписки ()](template-functions-resource.md#subscription) **не** поддерживается.
* Функции [reference()](template-functions-resource.md#reference) и [list()](template-functions-resource.md#list) поддерживаются.
* Используйте функцию [tenantResourceId()](template-functions-resource.md#tenantresourceid) для получения идентификатора ресурсов для ресурсов, развернутых на уровне арендатора.

  Например, чтобы получить идентификатор ресурсов для определения политики, используйте:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Идентификатор возвращенного ресурса имеет следующий формат:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Создание группы управления

[Следующий шаблон](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) создает группу управления.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>Назначение роли

[Следующий шаблон](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) назначает роль в области арендатора.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать о назначении ролей, [см. Управление доступом к ресурсам Azure с помощью шаблонов RBAC и azure Resource Manager.](../../role-based-access-control/role-assignments-template.md)
* Вы также можете развертывать шаблоны на [уровне подписки](deploy-to-subscription.md) или [уровня группы управления.](deploy-to-management-group.md)
