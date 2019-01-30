---
title: Программное создание политик и просмотр данных о соответствии
description: В этой статье описано программное создание и управление политиками для службы "Политика Azure".
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: adeb963333ffc2b587d7468eb357fab8dc4d6bbe
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847056"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Программное создание политик и просмотр данных о соответствии

В этой статье описано программное создание и управление политиками. Определения политик обеспечивают соблюдение различных правил и выполнение действий в ваших ресурсах. Принудительное применение обеспечивает соответствие ресурсов корпоративным стандартам и соглашениям об уровне обслуживания.

Сведения о соответствии см. в статье [Получение данных о соответствии](getting-compliance-data.md).

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к работе, убедитесь, что у вас есть следующие необходимые компоненты.

1. Установите [ARMClient](https://github.com/projectkudu/ARMClient), если его у вас еще нет. Это средство, которое отправляет HTTP-запросы к API-интерфейсам на основе Azure Resource Manager.

1. Обновите свой модуль Azure PowerShell до последней версии. Дополнительные сведения см. в статье [Install the Azure PowerShell module](/powershell/azure/install-az-ps) (Установка модуля Azure PowerShell). Дополнительные сведения о последней версии см. по ссылке [для Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Зарегистрируйте поставщик ресурсов Policy Insights с помощью Azure PowerShell, чтобы связать подписку с поставщиком ресурсов. Чтобы сделать это, необходимо иметь разрешение на регистрацию поставщика ресурсов. Эта операция включается в роли участника и владельца. Выполните указанную ниже команду для регистрации поставщика ресурсов.

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

   Параметр **Scope** в `New-AzPolicyAssignment` также работает с подписками и группами управления. Параметр использует полный путь к ресурсу, возвращаемый свойством **ResourceId** в `Get-AzResourceGroup`. Формат параметра **Scope** для каждого контейнера приведен ниже.
   Замените `{rgName}`, `{subId}` и `{mgName}` именем вашей группы ресурсов, идентификатором подписки и именем группы управления соответственно.

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

   ```
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Замените {subscriptionId} идентификатором своей подписки или {managementGroupId} идентификатором своей [группы управления](../../management-groups/overview.md).

   Дополнительные сведения о структуре запроса см. в статье [Policy Definitions - Create Or Update](/rest/api/resources/policydefinitions/createorupdate) (Создание и обновление определений политик) и [Policy Set Definitions - Create Or Update At Management Group](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) (Создание и обновление определений политик в группе управления).

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

   ```
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

1. Чтобы создать определение политики, выполните следующую команду:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

1. Чтобы создать назначение политики, выполните следующую команду: Замените данные в символах &lt;&gt; на собственные значения.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

Вы можете получить идентификатор определения политики с помощью следующей команды в PowerShell.

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Идентификатор определения политики для созданного вами определения должен выглядеть следующим образом:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Дополнительные сведения об управлении политиками ресурсов с помощью Azure CLI см. в [этой статье](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о командах и запросах, используемых в этой статье, см. в следующих ресурсах.

- [Ресурсы REST API Azure](/rest/api/resources/)
- [Модули Azure PowerShell](/powershell/module/az.resources/#policies).
- [Команды Azure CLI для роботы с политикой](/cli/azure/policy?view=azure-cli-latest)
- [Policy Insights](/rest/api/policy-insights)
- [Упорядочение ресурсов с помощью групп управления Azure](../../management-groups/overview.md)