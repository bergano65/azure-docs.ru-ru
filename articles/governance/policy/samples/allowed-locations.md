---
title: Пример допустимых расположений
description: В этом примере определения политики необходимо, чтобы все ресурсы развертывались в допустимых расположениях.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 70e1b2b20c4846626121dec190dff220a154b81b
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341357"
---
# <a name="sample---allowed-region-locations"></a>Пример допустимых расположений

Эта политика позволяет ограничить расположения, которые ваша организация может указать при развертывании ресурсов. Используется для соблюдения географических требований. Исключает группы ресурсов, каталоги Microsoft.AzureActiveDirectory/b2cDirectories и ресурсы, в которых используется "глобальный" регион. Вы можете указать массив допустимых расположений.

Развернуть этот образец политики можно с помощью следующих средств.

- [портал Azure](#azure-portal).
- [Azure PowerShell](#azure-powershell)
- [Интерфейс командной строки Azure](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Пример политики

### <a name="policy-definition"></a>Определение политики

Полное описание политики JSON, использующееся API REST, кнопками «Развернуть в Azure» и вручную на портале.

[!code-json[full](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.json "Allowed locations")]

> [!NOTE]
> Если создавать политику на портале вручную, используйте вышеизложенные части **properties.parameters** и **properties.policyRule**. Создайте программу-оболочку двух разделов вместе с фигурными скобками `{}` для внесения допустимых данных JSON.

### <a name="policy-rules"></a>Правила политики

JSON определяет правила политики, используемые Azure CLI и Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Параметры политики

JSON определяет параметры политики, используемые Azure CLI и Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Параметры

|ИМЯ |type |Поле |ОПИСАНИЕ |
|---|---|---|---|
|listOfAllowedLocations |Массив, |Расположения |Список допустимых расположений.|

При создании задания через PowerShell или Azure CLI значения параметров могут передаваться как JSON или в строке, или через файл с использованием `-PolicyParameter` (PowerShell) или `--params` (Azure CLI).
PowerShell также поддерживает `-PolicyParameterObject`, требующий передать командлету хэш-таблицу имен и значений, где **Имя** является именем параметра, а **значение** — отдельным значением или массивом значений, передаваемых во время назначения.

В этом примере параметра разрешены только расположения _eastus2_ и _westus_.

```json
{
    "listOfAllowedLocations": {
        "value": [
            "eastus2",
            "westus"
        ]
    }
}
```

## <a name="azure-portal"></a>Портал Azure

[![Развертывание в Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)
[![Развертывание в Azure для государственных организаций](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Развертывание с помощью Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "allowed-locations" -DisplayName "Allowed locations" -description "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' -Mode Indexed

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'allowed-locations-assignment' -DisplayName 'Allowed locations Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
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
definition=$(az policy definition create --name 'allowed-locations' --display-name 'Allowed locations' --description 'This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' --mode Indexed)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-locations-assignment' --display-name 'Allowed locations Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
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
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
  ```

- Создайте назначения политики (область группы ресурсов).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

  Используйте следующий пример JSON для текста запроса.

  ```json
  {
      "properties": {
          "displayName": "Allowed locations Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-locations",
          "parameters": {
              "listOfAllowedLocations": {
                  "value": [
                      "eastus2",
                      "westus"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Удаление с помощью REST API

- Удаление назначения политики

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

- Удаление определения политики

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
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