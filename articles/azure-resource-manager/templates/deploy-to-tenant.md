---
title: Развертывание ресурсов в клиенте
description: В этой статье объясняется, как развертывать ресурсы в клиенте в шаблоне Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 854ccbd43509b6c0b5a04357844c78c32b7e6396
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668695"
---
# <a name="tenant-deployments-with-arm-templates"></a>Развертывание клиентов с помощью шаблонов ARM

По мере развития вашей организации может потребоваться определить и назначить [политики](../../governance/policy/overview.md) или [Управление доступом на основе ролей Azure (Azure RBAC)](../../role-based-access-control/overview.md) в клиенте Azure AD. С помощью шаблонов уровня клиента можно декларативно применять политики и назначать роли на глобальном уровне.

## <a name="supported-resources"></a>Поддерживаемые ресурсы

Не все типы ресурсов можно развернуть на уровне клиента. В этом разделе перечислены поддерживаемые типы ресурсов.

Для политик Azure используйте:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments);
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions);
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions);

Для управления доступом на основе ролей Azure (Azure RBAC) используйте:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments);

Для вложенных шаблонов, которые развертываются в группах управления, подписках или группах ресурсов, используйте:

* [размещения](/azure/templates/microsoft.resources/deployments)

Для создания групп управления используйте:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Для управления затратами используйте:

* [биллингпрофилес](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [водим](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [инвоицесектионс](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

## <a name="schema"></a>схема

Схема, используемая для развертываний клиентов, отличается от схемы развертываний группы ресурсов.

Для шаблонов используйте:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

Схема для файла параметров одинакова для всех областей развертывания. Для файлов параметров используйте:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Для Azure CLI используйте [az deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Для Azure PowerShell используйте [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

Более подробные сведения о командах и параметрах развертывания для развертывания шаблонов ARM см. в следующих статьях:

* [Развертывание ресурсов с помощью шаблонов ARM и портал Azure](deploy-portal.md)
* [Развертывание ресурсов с помощью шаблонов ARM и Azure CLI](deploy-cli.md)
* [Развертывание ресурсов с помощью шаблонов ARM и Azure PowerShell](deploy-powershell.md)
* [Развертывание ресурсов с помощью шаблонов ARM и Azure Resource Manager REST API](deploy-rest.md)
* [Использование кнопки развертывания для развертывания шаблонов из репозитория GitHub](deploy-to-azure-button.md)
* [Развертывание шаблонов ARM из Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Области развертывания

При развертывании в группе управления можно развернуть ресурсы в:

* Клиент
* группы управления в клиенте
* subscriptions
* группы ресурсов (с помощью двух вложенных развертываний)
* [ресурсы расширения](scope-extension-resources.md) можно применять к ресурсам

Пользователь, развертывающий шаблон, должен иметь доступ к указанной области.

В этом разделе показано, как указать различные области. Эти различные области можно объединить в один шаблон.

### <a name="scope-to-tenant"></a>Область для клиента

Ресурсы, определенные в разделе ресурсов шаблона, применяются к клиенту.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Область в группе управления

Чтобы выбрать целевую группу управления в клиенте, добавьте вложенное развертывание и укажите `scope` свойство.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,22":::

### <a name="scope-to-subscription"></a>Область действия для подписки

Вы также можете ориентироваться на подписки в клиенте. Пользователь, развертывающий шаблон, должен иметь доступ к указанной области.

Чтобы назначить подписку в клиенте, используйте вложенное развертывание и `subscriptionId` свойство.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="10,18":::

## <a name="deployment-location-and-name"></a>Расположение и имя развертывания

Для развертываний на уровне клиента необходимо указать расположение для развертывания. Расположение развертывания отделено от расположения развертываемых ресурсов. В расположении развертывания указывается место хранения данных развертывания.

Можно указать имя развертывания или использовать имя развертывания по умолчанию. Имя по умолчанию — это имя файла шаблона. Например, развернув шаблон с именем **azuredeploy.json** создается имя развертывания по умолчанию **azuredeploy** .

Для каждого имени развертывания расположение остается неизменным. Нельзя создать развертывание в одном расположении, если в другом уже есть развертывание с таким же именем. Если появится код ошибки `InvalidDeploymentLocation`, используйте другое имя или то же расположение, что и для предыдущего развертывания с этим именем.

## <a name="create-management-group"></a>Создание группы управления

В [следующем примере](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg) создается группа управления:

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

В [следующем шаблоне](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment) назначается роль в области клиента:

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

* Дополнительные сведения о назначении ролей см. в статье [Добавление назначений ролей Azure с помощью шаблонов Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Кроме того, можно развертывать шаблоны на уровне [подписки](deploy-to-subscription.md) или [группы управления](deploy-to-management-group.md).
