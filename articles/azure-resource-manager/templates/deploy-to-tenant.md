---
title: Развертывание ресурсов в клиенте
description: Описывает развертывание ресурсов в области клиента в шаблоне Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 64090f1a0bac4b2b5f18d8dec14be0c3b051ac17
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968882"
---
# <a name="create-resources-at-the-tenant-level"></a>Создание ресурсов на уровне клиента

По мере разработки вашей организации может потребоваться определить и назначить [политики](../../governance/policy/overview.md) или [элементы управления доступом на основе ролей](../../role-based-access-control/overview.md) в клиенте Azure AD. С помощью шаблонов уровня клиента можно декларативно применять политики и назначать роли на глобальном уровне.

## <a name="supported-resources"></a>Поддерживаемые ресурсы

На уровне клиента можно развернуть следующие типы ресурсов:

* [развертывания](/azure/templates/microsoft.resources/deployments) — для вложенных шаблонов, которые развертываются в группах управления или подписках.
* [полициассигнментс](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [полицисетдефинитионс](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

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

Участник, развертывающий шаблон, должен иметь разрешения на создание ресурсов в области клиента. Участник должен иметь разрешение на выполнение действий развертывания (`Microsoft.Resources/deployments/*`) и создание ресурсов, определенных в шаблоне. Например, чтобы создать группу управления, участник должен иметь разрешение участника в области клиента. Чтобы создать назначения ролей, участник должен иметь разрешение Owner.

Глобальный администратор Azure Active Directory не имеет разрешения на назначение ролей автоматически. Чтобы включить развертывания шаблонов в области клиента, глобальный администратор должен выполнить следующие действия.

1. Повысить уровень доступа к учетной записи, чтобы глобальный администратор мог назначать роли. Дополнительные сведения см. [в статье повышение уровня доступа для управления всеми подписками Azure и группами управления](../../role-based-access-control/elevate-access-global-admin.md).

1. Назначение владельца или участника участнику, который должен развертывать шаблоны.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

У участника теперь есть необходимые разрешения для развертывания шаблона.

## <a name="deployment-commands"></a>Команды развертывания

Команды для развертываний клиентов отличаются от команд для развертываний групп ресурсов.

Для Azure PowerShell используйте [New-азтенантдеплоймент](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Для REST API используйте [развертывания — создание или обновление в области клиента](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Расположение и имя развертывания

Для развертываний на уровне клиента необходимо указать расположение для развертывания. Расположение развертывания отделено от расположения развертываемых ресурсов. В расположении развертывания указывается место хранения данных развертывания.

Можно указать имя развертывания или использовать имя развертывания по умолчанию. Имя по умолчанию — это имя файла шаблона. Например, развернув шаблон с именем **azuredeploy.json** создается имя развертывания по умолчанию **azuredeploy**.

Для каждого имени развертывания расположение является неизменяемым. Нельзя создать развертывание в одном месте, если существует развертывание с тем же именем в другом расположении. Если появится код ошибки `InvalidDeploymentLocation`, используйте другое имя или то же расположение, что и для предыдущего развертывания с этим именем.

## <a name="use-template-functions"></a>Использование функций шаблонов

При использовании функций шаблона необходимо учитывать некоторые важные моменты при развертывании клиентов.

* Функция [resourceGroup()](template-functions-resource.md#resourcegroup)**не** поддерживается.
* Функция [Subscription ()](template-functions-resource.md#subscription) **не** поддерживается.
* Функции [reference()](template-functions-resource.md#reference) и [list()](template-functions-resource.md#list) поддерживаются.
* Используйте функцию [тенантресаурцеид ()](template-functions-resource.md#tenantresourceid) , чтобы получить идентификатор ресурса для ресурсов, развернутых на уровне клиента.

  Например, чтобы получить идентификатор ресурса для определения политики, используйте:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Возвращенный идентификатор ресурса имеет следующий формат:
  
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

[Следующий шаблон](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) назначает роль в области клиента.

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

* Дополнительные сведения о назначении ролей см. в статье [Управление доступом к ресурсам Azure с помощью RBAC и шаблонов Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Можно также развертывать шаблоны на уровне [подписки](deploy-to-subscription.md) или [группы управления](deploy-to-management-group.md).
