---
title: Создание политики для аудита ресурсов с помощью Azure CLI
description: Создание назначения в службе "Политика Azure", позволяющего определить ресурсы, которые не соответствуют требованиям, с помощью Azure CLI.
ms.date: 01/23/2019
ms.topic: quickstart
ms.openlocfilehash: 589038bb2b5e96c252e19fc0552d4f9ac19dc83a
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960226"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Краткое руководство. Создание назначения политики для идентификации ресурсов, не соответствующих требованиям, с помощью Azure CLI

Чтобы понять, соответствуют ли ресурсы требованиям в Azure, прежде всего нужно определить их состояние.
В этом кратком руководстве описано, как создать назначение политики для определения виртуальных машин, которые не используют управляемые диски.

Завершив работу, вы узнаете, какие виртуальные машины не используют управляемые диски, так как *не соответствуют* назначению политики.

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью сценариев. В этом руководстве используется Azure CLI для создания назначения политики для определения в среде Azure ресурсов, не соответствующих требованиям.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Для этого руководства вам потребуется Azure CLI 2.0.4 или более поздней версии, чтобы установить и использовать интерфейс командной строки локально. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Предварительные требования

Зарегистрируйте поставщика ресурсов Insights в Политике Azure с помощью Azure CLI. Регистрация поставщика ресурсов необходима для надлежащей работы вашей подписки с этим поставщиком. Чтобы зарегистрировать поставщик ресурсов, необходимо разрешение на регистрацию для поставщика ресурсов. Эта операция включается в роли участника и владельца. Выполните указанную ниже команду для регистрации поставщика ресурсов.

```azurecli-interactive
az provider register --namespace 'Microsoft.PolicyInsights'
```

Дополнительные сведения о регистрации и просмотре поставщиков ресурсов см. в статье [Поставщики и типы ресурсов](../../azure-resource-manager/resource-manager-supported-services.md).

Установите [ARMClient](https://github.com/projectkudu/ARMClient), если его у вас еще нет. Это средство, которое отправляет HTTP-запросы к API-интерфейсам на основе Azure Resource Manager.

## <a name="create-a-policy-assignment"></a>Создание назначения политики

С помощью этого краткого руководства вы создадите назначение политики и назначите определение **Audit VMs that do not use managed disks** (Аудит виртуальных машин, которые не используют управляемые диски). Это определение политики идентифицирует ресурсы, которые не соответствуют заданным в нем условиям.

Чтобы создать назначение политики, выполните следующую команду:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

В указанной выше команде используются следующие сведения:

- **Name** — фактическое имя назначения. В этом примере использовалось имя *audit-vm-manageddisks*.
- **DisplayName** — отображаемое имя назначения политики. В этом случае используется определение *аудита виртуальных машин без назначения управляемых дисков*.
- **Policy** — идентификатор определения политики, на основе которой вы создаете назначение. В нашем случае это идентификатор определения политики *аудита виртуальных машин, которые не используют управляемые диски*. Чтобы получить идентификатор определения политики, выполните следующую команду: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Scope.** Область определяет, к каким ресурсам или группе ресурсов принудительно применяется назначение политики. Политика может назначаться разным ресурсам: от подписки до групп ресурсов. Обязательно замените значение &lt;scope&gt; именем своей группы ресурсов.

## <a name="identify-non-compliant-resources"></a>Выявление несоответствующих ресурсов

Чтобы просмотреть ресурсы, которые не соответствуют новому назначению, получите идентификатор назначения политики, выполнив следующие команды:

```azurepowershell-interactive
$policyAssignment = Get-AzPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Дополнительные сведения об идентификаторах назначения политики см. в описании командлета [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment).

Теперь выполните следующую команду, чтобы вывести идентификаторы несовместимых ресурсов в JSON-файле:

```console
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

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы назначили определение политики для идентификации ресурсов, не соответствующих требованиям, в среде Azure.

Следующее руководство серии содержит сведения о назначении политик для проверки новых ресурсов на соответствие требованиям:

> [!div class="nextstepaction"]
> [Создание политик и управление ими](./tutorials/create-and-manage.md)