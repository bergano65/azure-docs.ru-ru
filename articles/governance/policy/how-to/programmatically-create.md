---
title: Программное создание политик
description: This article walks you through programmatically creating and managing policies for Azure Policy with Azure CLI, Azure PowerShell, and REST API.
ms.date: 01/31/2019
ms.topic: conceptual
ms.openlocfilehash: 98af714e5aaf8e103b81e77c9960589fa0ee6b77
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463540"
---
# <a name="programmatically-create-policies"></a>Программное создание политик

В этой статье описано программное создание и управление политиками. Azure Policy definitions enforce different rules and effects over your resources. Принудительное применение обеспечивает соответствие ресурсов корпоративным стандартам и соглашениям об уровне обслуживания.

Сведения о соответствии см. в статье [Получение данных о соответствии](get-compliance-data.md).

## <a name="prerequisites"></a>Технические условия

Прежде чем приступить к работе, убедитесь, что у вас есть следующие необходимые компоненты.

1. Установите [ARMClient](https://github.com/projectkudu/ARMClient), если его у вас еще нет. Это средство, которое отправляет HTTP-запросы к API-интерфейсам на основе Azure Resource Manager.

1. Обновите свой модуль Azure PowerShell до последней версии. Дополнительные сведения см. в статье [Install the Azure PowerShell module](/powershell/azure/install-az-ps) (Установка модуля Azure PowerShell). Дополнительные сведения о последней версии см. по ссылке [для Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Register the Azure Policy Insights resource provider using Azure PowerShell to validate that your subscription works with the resource provider. Чтобы сделать это, необходимо иметь разрешение на регистрацию поставщика ресурсов. Эта операция включается в роли участника и владельца. Выполните указанную ниже команду для регистрации поставщика ресурсов.

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Дополнительные сведения о регистрации и просмотре поставщиков ресурсов см. в статье [Поставщики и типы ресурсов](../../../azure-resource-manager/resource-manager-supported-services.md).

1. Установите интерфейс командной строки Azure CLI, если это еще не сделано. Последнюю версию CLI см. в статье [Установка Azure CLI в Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Создание и назначение определения политики

Первый шаг для улучшения видимости ваших ресурсов — создать и назначить в них политики. Теперь необходимо научиться программно создавать и назначать политику. Пример политики проверяет учетные записи хранения, которые открыты для всех общедоступных сетей, используя запросы PowerShell, Azure CLI и HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Создание и назначение определения политики с помощью PowerShell

1. Используйте следующий фрагмент кода JSON, чтобы создать файл JSON с именем AuditStorageAccounts.json.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Дополнительные сведения о создании определения политики см. в статье [Структура определения службы "Политика Azure"](../concepts/definition-structure.md).

1. Выполните следующую команду, чтобы создать определение политики с использованием файла AuditStorageAccounts.json.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Эта команда создает определение политики с именем _Audit Storage Accounts Open to Public Networks_ (Аудит учетных записей хранения, открытых для общедоступных сетей).
   Дополнительные сведения о других параметрах, которые можно использовать, см. в статье о командлете [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   При вызове без параметров расположения `New-AzPolicyDefinition` по умолчанию сохраняет определение политики в выбранной подписке контекста сеансов. Чтобы сохранить определение в другом месте, используйте следующие параметры:

   - **SubscriptionId** — сохранение в другой подписке. Требуется значение _GUID_.
   - **ManagementGroupName** — сохранение в группе управления. Требуется значение _строки_.

1. После создания определения политики вы можете создать назначение политики, выполнив следующие команды:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Замените _ContosoRG_ именем вашей целевой группы ресурсов.

   The **Scope** parameter on `New-AzPolicyAssignment` works with management group, subscription, resource group, or a single resource. Параметр использует полный путь к ресурсу, возвращаемый свойством **ResourceId** в `Get-AzResourceGroup`. Формат параметра **Scope** для каждого контейнера приведен ниже. Замените `{rName}`, `{rgName}`, `{subId}` и `{mgName}` именем ресурса, именем группы ресурсов, идентификатором подписки и именем группы управления соответственно.
   Замените `{rType}` **типом ресурса**, таким как `Microsoft.Compute/virtualMachines` для виртуальной машины.

   - Ресурс: `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`.
   - Группа ресурсов — `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Подписка — `/subscriptions/{subId}/`
   - Группа управления — `/providers/Microsoft.Management/managementGroups/{mgName}`

Дополнительные сведения об управлении политиками ресурсов с помощью модуля PowerShell Azure Resource Manager см. в разделе [Политики](/powershell/module/az.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Создание и назначение определения политики с помощью ARMClient

Выполните указанные ниже действия, чтобы создать определение политики.

1. Скопируйте следующий фрагмент кода JSON, чтобы создать файл JSON. Вызовите файл на следующем шаге.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Создайте определение политики с использованием одного из следующих вызовов:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Замените {subscriptionId} идентификатором своей подписки или {managementGroupId} идентификатором своей [группы управления](../../management-groups/overview.md).

   For more information about the structure of the query, see [Azure Policy Definitions – Create or Update](/rest/api/resources/policydefinitions/createorupdate) and [Policy Definitions – Create or Update At Management Group](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Используйте следующую процедуру для создания назначения политики и назначьте определение политики на уровне группы ресурсов.

1. Скопируйте следующий фрагмент кода JSON, чтобы создать файл JSON назначения политики. Замените данные в символах &lt;&gt; на собственные значения.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Создайте назначение политики с использованием следующего вызова:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Замените данные в символах &lt;&gt; на собственные значения.

   Дополнительные сведения о том, как выполнять HTTP-запросы к REST API, см. в статье [Azure Resource Manager](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Создание и назначение определения политики с помощью Azure CLI

Чтобы создать определение политики, выполните указанные ниже действия.

1. Скопируйте следующий фрагмент кода JSON, чтобы создать файл JSON назначения политики.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Дополнительные сведения о создании определения политики см. в статье [Структура определения службы "Политика Azure"](../concepts/definition-structure.md).

1. Чтобы создать определение политики, выполните следующую команду:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Эта команда создает определение политики с именем _Audit Storage Accounts Open to Public Networks_ (Аудит учетных записей хранения, открытых для общедоступных сетей).
   Дополнительные сведения о других параметрах, которые можно использовать, см. в разделе о [az policy definition create](/cli/azure/policy/definition#az-policy-definition-create).

   При вызове без параметров расположения `az policy definition creation` по умолчанию сохраняет определение политики в выбранной подписке контекста сеансов. Чтобы сохранить определение в другом месте, используйте следующие параметры:

   - **--subscription** — сохранение в другой подписке. Требуется значение _GUID_ для идентификатора подписки или значение _строки_ для имени подписки.
   - **--management-group** — сохранение в группе управления. Требуется значение _строки_.

1. Чтобы создать назначение политики, выполните следующую команду: Замените данные в символах &lt;&gt; на собственные значения.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   Параметр **--scope** в `az policy assignment create` работает с группой управления, подпиской, группой ресурсов или отдельным ресурсом. Параметр использует полный путь к ресурсу. Формат параметра **--scope** для каждого контейнера приведен ниже. Замените `{rName}`, `{rgName}`, `{subId}` и `{mgName}` именем ресурса, именем группы ресурсов, идентификатором подписки и именем группы управления соответственно. Замените `{rType}` **типом ресурса**, таким как `Microsoft.Compute/virtualMachines` для виртуальной машины.

   - Ресурс: `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`.
   - Группа ресурсов — `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Подписка — `/subscriptions/{subID}`
   - Группа управления — `/providers/Microsoft.Management/managementGroups/{mgName}`

You can get the Azure Policy Definition ID by using PowerShell with the following command:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Идентификатор определения политики для созданного вами определения должен выглядеть следующим образом:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Дополнительные сведения об управлении политиками ресурсов с помощью Azure CLI см. в [этой статье](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о командах и запросах, используемых в этой статье, см. в следующих ресурсах.

- [Ресурсы REST API Azure](/rest/api/resources/)
- [Модули Azure PowerShell](/powershell/module/az.resources/#policies).
- [Команды Azure CLI для роботы с политикой](/cli/azure/policy?view=azure-cli-latest)
- [Azure Policy Insights resource provider REST API reference](/rest/api/policy-insights)
- [Организация ресурсов с помощью групп управления Azure.](../../management-groups/overview.md)