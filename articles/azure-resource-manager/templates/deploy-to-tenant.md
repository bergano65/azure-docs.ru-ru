---
title: Развертывание ресурсов в клиенте
description: В этой статье объясняется, как развертывать ресурсы в клиенте в шаблоне Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 8a3748c0948238b588a01f7d91780693a2c5bf3a
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930069"
---
# <a name="create-resources-at-the-tenant-level"></a>Создание ресурсов на уровне клиента

По мере развития вашей организации может потребоваться определить и назначить [политики](../../governance/policy/overview.md) или [элементы управления доступом на основе ролей](../../role-based-access-control/overview.md) в клиенте Azure AD. С помощью шаблонов уровня клиента можно декларативно применять политики и назначать роли на глобальном уровне.

## <a name="supported-resources"></a>Поддерживаемые ресурсы

На уровне клиента можно развернуть ресурсы следующих типов:

* [deployments](/azure/templates/microsoft.resources/deployments) — для вложенных шаблонов, которые развертываются в группах управления или подписках;
* managementGroups
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments);
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions);
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions);
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments);
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions).

### <a name="schema"></a>схема

Схема, используемая для развертываний клиентов, отличается от схемы развертываний группы ресурсов.

Для шаблонов используйте:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Схема для файла параметров одинакова для всех областей развертывания. Для файлов параметров используйте:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Требуемый доступ

У субъекта, развертывающего шаблон, должны быть разрешения на создание ресурсов в области клиента. У субъекта должно быть разрешение на действия развертывания (`Microsoft.Resources/deployments/*`) и создание ресурсов, определенных в шаблоне. Например, чтобы создать группу управления, у субъекта должно быть разрешение "Участник" в области клиента. Чтобы создавать назначения ролей, у участника должно быть разрешение "Владелец".

Глобальный администратор Azure Active Directory не получает разрешения на назначение ролей автоматически. Чтобы включить развертывания шаблонов в области клиента, глобальному администратору следует выполнить такие действия:

1. Повысить уровень доступа к учетной записи, чтобы глобальный администратор мог назначать роли. Дополнительные сведения см. в статье [Повышение прав доступа для управления всеми подписками Azure и группами управления](../../role-based-access-control/elevate-access-global-admin.md).

1. Назначьте роль "Владелец" или "Участник" субъекту, который будет развертывать шаблоны.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

У субъекта теперь есть необходимые разрешения для развертывания шаблона.

## <a name="deployment-commands"></a>Команды развертывания

Команды, используемые для развертываний клиентов, отличаются от команд для развертываний группы ресурсов.

Для Azure CLI используйте [az deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create):

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

Для REST API используйте инструкции в статье [Развертывания — создание или обновление в области клиента](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Расположение и имя развертывания

Для развертываний на уровне клиента необходимо указать расположение для развертывания. Расположение развертывания отделено от расположения развертываемых ресурсов. В расположении развертывания указывается место хранения данных развертывания.

Можно указать имя развертывания или использовать имя развертывания по умолчанию. Имя по умолчанию — это имя файла шаблона. Например, развернув шаблон с именем **azuredeploy.json** создается имя развертывания по умолчанию **azuredeploy**.

Для каждого имени развертывания расположение остается неизменным. Нельзя создать развертывание в одном расположении, если в другом уже есть развертывание с таким же именем. Если появится код ошибки `InvalidDeploymentLocation`, используйте другое имя или то же расположение, что и для предыдущего развертывания с этим именем.

## <a name="use-template-functions"></a>Использование функций шаблонов

Важные рекомендации при использовании функций шаблонов для развертываний на уровне клиента:

* Функция [resourceGroup()](template-functions-resource.md#resourcegroup)**не** поддерживается.
* Функция [subscription()](template-functions-resource.md#subscription) **не** поддерживается.
* Функции [reference()](template-functions-resource.md#reference) и [list()](template-functions-resource.md#list) поддерживаются.
* Используйте функцию [tenantResourceId()](template-functions-resource.md#tenantresourceid), чтобы получить идентификатор ресурса для ресурсов, развернутых на уровне клиента.

  Например, чтобы получить идентификатор ресурса для определения политики, используйте:

  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```

  Возвращенный идентификатор ресурса имеет следующий формат:

  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Создание группы управления

В [следующем примере](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) создается группа управления:

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

В [следующем шаблоне](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) назначается роль в области клиента:

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

* Сведения о назначении ролей см. в статье об [управлении доступом к ресурсам Azure с помощью RBAC и шаблонов Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Кроме того, можно развертывать шаблоны на уровне [подписки](deploy-to-subscription.md) или [группы управления](deploy-to-management-group.md).
