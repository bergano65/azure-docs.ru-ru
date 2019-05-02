---
title: Пример — выявление хранилищ Key Vault без конечных точек виртуальной сети
description: В этом примере определения политики выполняется проверка хранилищ Key Vault с целью обнаружить экземпляры без конечных точек служб для виртуальной сети.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: bc5ce4a6a2e52ed8d21de8db8da1f815293b61f7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276377"
---
# <a name="sample---key-vault-vaults-with-no-virtual-network-endpoints"></a>Пример — хранилища Key Vault без конечных точек виртуальной сети

Эта политика проверяет хранилища Key Vault для обнаружения экземпляров без конечных точек виртуальной сети. Используйте эту политику, чтобы обеспечить соблюдение требований к безопасности. Дополнительные сведения см. в статье [Конечные точки служб для виртуальной сети для Azure Key Vault](../../../key-vault/key-vault-overview-vnet-service-endpoints.md).

Развернуть этот образец политики можно с помощью следующих средств.

- [портал Azure](#azure-portal).
- [Azure PowerShell](#azure-powershell)
- [Интерфейс командной строки Azure](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Пример политики

### <a name="policy-definition"></a>Определение политики

Полное описание политики JSON, использующееся API REST, кнопками «Развернуть в Azure» и вручную на портале.

[!code-json[full](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.json "KeyVault vnet rules")]

> [!NOTE]
> Если создавать политику на портале вручную, используйте вышеизложенные части **properties.parameters** и **properties.policyRule**. Создайте программу-оболочку двух разделов вместе с фигурными скобками `{}` для внесения допустимых данных JSON.

### <a name="policy-rules"></a>Правила политики

JSON определяет правила политики, используемые Azure CLI и Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Параметры политики

В этом примере определения политики нет заданных параметров.

## <a name="azure-portal"></a>Портал Azure

[![Развертывание примера политики в Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)
[![Deploy the Policy sample to Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Развертывание с помощью Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "audit-keyvault-vnet-rules" -DisplayName "Audit if Key Vault has no virtual network rules" -description "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'audit-keyvault-vnet-rules-assignment' -DisplayName 'Audit Key Vault Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition
```

### <a name="remove-with-azure-powershell"></a>Удаление с помощью Azure PowerShell

Чтобы удалить предыдущее назначение и определение, выполните следующую команду.

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Пояснение Azure PowerShell

Чтобы развернуть и удалить скрипты используйте следующие команды. Для каждой команды в следующей таблице приведены ссылки на соответствующую документацию:

| Команда | Примечания |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Создается новое определение политики Azure. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Получение одной группы ресурсов. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Создается новое назначение политики Azure. В этом примере политики предоставляется определение, но она также может взять на себя инициативу. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Удаление существующего назначения политики Azure. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Удаление существующего определения политики Azure. |

## <a name="azure-cli"></a>Инфраструктура CLI Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'audit-keyvault-vnet-rules' --display-name 'Audit if Key Vault has no virtual network rules' --description 'Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'audit-keyvault-vnet-rules-assignment' --display-name 'Audit Key Vault Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r`)
```

### <a name="remove-with-azure-cli"></a>Удаление с помощью интерфейса командной строки Azure

Чтобы удалить предыдущее назначение и определение, выполните следующую команду.

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Пояснение Azure CLI

| Команда | Примечания |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Создается новое определение политики Azure. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Получение одной группы ресурсов. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Создается новое назначение политики Azure. В этом примере политики предоставляется определение, но она также может взять на себя инициативу. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Удаление существующего назначения политики Azure. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Удаление существующего определения политики Azure. |

## <a name="rest-api"></a>REST API

Существует несколько средств, которые могут использоваться для взаимодействия с диспетчером ресурсов REST API, такие как [ARMClient](https://github.com/projectkudu/ARMClient) или PowerShell.

### <a name="deploy-with-rest-api"></a>Развертывание с помощью REST API

- Создайте определения политики (область подписки). Используйте [определения политики](#policy-definition) JSON для текста запроса.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

- Создайте назначения политики (область группы ресурсов).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

  Используйте следующий пример JSON для текста запроса.

  ```json
  {
      "properties": {
          "displayName": "Audit Key Vault Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules"
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Удаление с помощью REST API

- Удаление назначения политики

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

- Удаление определения политики

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>Описание REST API

| Service | Группа | Операция | Примечания |
|---|---|---|---|
| Управление ресурсами | Определения политик | [Создание](/rest/api/resources/policydefinitions/createorupdate) | Создается новое определение политики Azure в подписке. Альтернативный вариант: [создание группы управления](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Управление ресурсами | Назначения политик | [Создание](/rest/api/resources/policyassignments/create) | Создается новое назначение политики Azure. В этом примере политики предоставляется определение, но она также может взять на себя инициативу. |
| Управление ресурсами | Назначения политик | [Удалить](/rest/api/resources/policyassignments/delete) | Удаление существующего назначения политики Azure. |
| Управление ресурсами | Определения политик | [Удалить](/rest/api/resources/policydefinitions/delete) | Удаление существующего определения политики Azure. Альтернативный вариант: [удаление группы управления](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Дополнительная информация

- Просмотр дополнительных [примеров для Политики Azure](index.md)
- Просмотр [Структура определения службы "Политика Azure"](../concepts/definition-structure.md)