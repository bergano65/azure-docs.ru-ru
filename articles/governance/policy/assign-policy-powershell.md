---
title: Создание политики для идентификации ресурсов, не соответствующих требованиям, с помощью Azure PowerShell
description: Создание назначения в службе "Политика Azure", позволяющего определить ресурсы, которые не соответствуют требованиям, с помощью Azure PowerShell.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 32fe811c80fd34b4ea3390a3f46a1d36aba7534e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310714"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Создание назначения политики для идентификации ресурсов, не соответствующих требованиям, с помощью Azure PowerShell

Чтобы понять, соответствуют ли ресурсы требованиям в Azure, прежде всего нужно определить их состояние. В рамках этого краткого руководства вы создадите назначение политики для выявления виртуальных машин, которые не используют управляемые диски. После выполнения задач в руководстве вы сможете выявлять виртуальные машины, которые *не соответствуют* назначению политики.

Модуль AzureRM PowerShell используется для создания и администрирования ресурсов Azure с использованием командной строки или скриптов. В этом руководстве объясняется, как при помощи AzureRM создавать назначение политики. Политика выявляет в среде Azure ресурсы, не соответствующие требованиям.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

- Установите [ARMClient](https://github.com/projectkudu/ARMClient), если его у вас еще нет. Это средство, которое отправляет HTTP-запросы к API-интерфейсам на основе Azure Resource Manager.
- Перед началом работы убедитесь, что установлена последняя версия PowerShell. Дополнительные сведения см. в статье [Общие сведения об Azure PowerShell](/powershell/azureps-cmdlets-docs).
- Обновите свой модуль AzureRM PowerShell до последней версии. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).
- Зарегистрируйте поставщик ресурсов Policy Insights с помощью Azure PowerShell. Регистрация поставщика ресурсов необходима для надлежащей работы вашей подписки с этим поставщиком. Чтобы зарегистрировать поставщик ресурсов, необходимо разрешение на регистрацию для поставщика ресурсов. Эта операция включается в роли участника и владельца. Выполните указанную ниже команду для регистрации поставщика ресурсов.

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Дополнительные сведения о регистрации и просмотре поставщиков ресурсов см. в статье [Поставщики и типы ресурсов](../../azure-resource-manager/resource-manager-supported-services.md).

## <a name="create-a-policy-assignment"></a>Создание назначения политики

В рамках этого краткого руководства вы создадите назначение политики и назначите определение *аудита виртуальных машин без управляемых дисков*. Это определение политики идентифицирует ресурсы, которые не соответствуют заданным в нем условиям.

Чтобы создать назначение политики, выполните следующие команды:

```azurepowershell-interactive
$rg = Get-AzureRmResourceGroup -Name '<resourceGroupName>'
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }
New-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

В указанных выше командах используются следующие сведения:

- **Name** — фактическое имя назначения.  В этом примере использовалось имя *audit-vm-manageddisks*.
- **DisplayName** — отображаемое имя назначения политики. В этом случае используется определение *аудита виртуальных машин без назначения управляемых дисков*.
- **Definition.** Определение политики, на основе которого вы создаете назначение. В нашем случае это идентификатор определения политики *аудита виртуальных машин, которые не используют управляемые диски*.
- **Scope.** Область определяет, к каким ресурсам или группе ресурсов принудительно применяется назначение политики. Политика может назначаться разным ресурсам: от подписки до групп ресурсов. Обязательно замените значение &lt;scope&gt; именем своей группы ресурсов.

Теперь все готово к выявлению ресурсов, которые не соответствуют требованиям, что позволит оценить состояние соответствия в среде.

## <a name="identify-non-compliant-resources"></a>Выявление несоответствующих ресурсов

Используйте приведенные ниже сведения для идентификации ресурсов, которые не соответствуют созданному вами назначению политики. Выполните следующие команды:

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Дополнительные сведения об идентификаторах назначения политики см. в описании командлета [Get-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Теперь выполните следующую команду, чтобы вывести идентификаторы несовместимых ресурсов в JSON-файле:

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Результаты должны выглядеть примерно так:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Результаты похожи на содержимое списка **несовместимых ресурсов** в представлении портала Azure.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить созданное назначение, выполните следующую команду:

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы назначили определение политики для идентификации ресурсов, не соответствующих требованиям, в среде Azure.

Следующее руководство серии содержит сведения о назначении политик для проверки новых ресурсов на соответствие требованиям:

> [!div class="nextstepaction"]
> [Создание политик и управление ими](./tutorials/create-and-manage.md)