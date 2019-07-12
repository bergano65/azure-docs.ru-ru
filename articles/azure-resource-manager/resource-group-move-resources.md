---
title: Перемещение ресурсов Azure в новую группу ресурсов или подписку | Документация Майкрософт
description: Перемещайте ресурсы в новую группу ресурсов или новую подписку с помощью Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 01ec8facf2771de9ec01b9470521340a59ee4d0d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721382"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Перемещение ресурсов в новую группу ресурсов или подписку

В этой статье показано, как переместить ресурсы Azure в другую подписку Azure или другую группу ресурсов в той же подписке. Для перемещения ресурса можно использовать портал Azure, Azure PowerShell, интерфейс командной строки Azure или REST API.

Группа источника и группа назначения блокируются на время операции перемещения. Операции записи и удаления для групп ресурсов блокируются до завершения перемещения. Эта блокировка означает, что невозможно добавить, обновить или удалить ресурсы в группах ресурсов, но не означает, что эти ресурсы закреплены. Например, если переместить в новую группу ресурсов экземпляр SQL Server и его базу данных, то приложение, использующее эту базу данных, не будет испытывать простоев в работе. Оно по-прежнему сможет считывать данные из базы данных и записывать их в нее.

Перемещение ресурса всего лишь перемещается в новую группу ресурсов или подписки. Расположение ресурса не изменяется.

## <a name="checklist-before-moving-resources"></a>Рекомендации перед перемещением ресурсов

Вот некоторые важные особенности, которые следует учитывать перед перемещением ресурса. Проверив эти условия, можно избежать ошибок.

1. Ресурсы, которые вы хотите переместить должен поддерживать операции перемещения. Список, из которых ресурсы поддерживают перемещение, см. в разделе [перемещения операции благодаря поддержке ресурсов](move-support-resources.md).

1. Некоторые службы имеют определенные ограничения или требования, при перемещении ресурсов. Если вы уже, переместив следующие службы, проверьте этой рекомендации перед перемещением.

   * [Руководство по перемещения служб приложений](./move-limitations/app-service-move-limitations.md)
   * [Руководство по перемещения служб Azure DevOps](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Рекомендации перемещения модели классического развертывания](./move-limitations/classic-model-move-limitations.md) -классической вычислений, классического хранилища, классических виртуальных сетей и облачных служб
   * [Руководство по перемещения служб восстановления](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Руководство по перемещение виртуальных машин](./move-limitations/virtual-machines-move-limitations.md)
   * [Виртуальные сети переместить рекомендации](./move-limitations/virtual-network-move-limitations.md)

1. Исходная и целевая подписки должны быть активными. Если у вас возникла проблема при включении учетной записи, которая была отключена, [создайте запрос на поддержку Azure](../azure-supportability/how-to-create-azure-support-request.md). Выберите тип проблемы **Управление подпиской**.

1. Исходная и целевая подписка должны существовать в пределах одного [клиента Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Используя Azure PowerShell или командную строку Azure, можно убедиться, что обе подписки имеют один и тот же идентификатор клиента.

   Для Azure PowerShell:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Для интерфейса командной строки Azure:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Если идентификаторы клиентов исходных и целевых подписок не совпадают, используйте следующие методы для выверки идентификаторов клиентов:

   * [Передача прав владения подпиской Azure другой учетной записи](../billing/billing-subscription-transfer.md)
   * [Как связать или добавить подписку Azure в Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Подписка назначения должна быть зарегистрирована для перемещаемого поставщика ресурсов. В противном случае отображается сообщение о том, что **подписка не зарегистрирована для типа ресурса**. Эта ошибка может возникнуть при перемещении ресурса в новую подписку, которая никогда не использовалась с этим типом ресурса.

   Для PowerShell используйте следующие команды, чтобы получить состояние регистрации:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Чтобы зарегистрировать поставщик ресурсов, воспользуйтесь командой:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Для Azure CLI используйте следующие команды, чтобы получить состояние регистрации:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Чтобы зарегистрировать поставщик ресурсов, воспользуйтесь командой:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Учетная запись, используемая для перемещения ресурсов, должна предоставлять по крайней мере следующие разрешения:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** для исходной группы ресурсов;
   * **Microsoft.Resources/subscriptions/resourceGroups/write** для целевой группы ресурсов.

1. Прежде чем перемещать ресурсы, проверьте квоты подписки, в которую вы перемещаете ресурсы. Если при перемещении ресурсов квота подписки будет превышена, возможно, вам придется запросить увеличение квоты. Полный список ограничений и способы запросить увеличение квоты см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).

## <a name="validate-move"></a>Проверка перемещения

[Операция проверки перемещения](/rest/api/resources/resources/validatemoveresources) позволяет проверить сценарий перемещения без фактического перемещения ресурсов. Эта операция используется для проверки, если будет выполнено перемещение. Проверка вызывается автоматически при отправке запроса на перемещение. Эта операция используется только в том случае, если необходимо предварительно определить результаты. Для запуска этой операции вам необходимо:

* имя исходной группы ресурсов;
* идентификатор ресурса целевой группы ресурсов;
* идентификатор каждого перемещаемого ресурса;
* [маркер доступа](/rest/api/azure/#acquire-an-access-token) для вашей учетной записи.

Отправьте следующий запрос:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

С текстом запроса:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Если запрос имеет правильный формат, операция возвращает следующее:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Код состояния 202 указывает, что запрос на проверку был принят, но еще не определено, завершится ли операция перемещения успешно. Значение `location` содержит URL-адрес, который используется для проверки состояния продолжительной операции.  

Чтобы проверить состояние, отправьте следующий запрос:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Пока операция все еще выполняется, вы по-прежнему получаете код состояния 202. Подождите в течение времени, указанного в значении `retry-after`, прежде чем повторять попытку. Если проверка операции перемещения завершена успешно, вы получите код состояния 204. Если проверка перемещения закончилась ошибкой, вы получите сообщение об ошибке, например:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Использование портала

Чтобы переместить ресурсы, выберите группу ресурсов, в которой они содержатся, а затем нажмите кнопку **Переместить**.

![перемещение ресурсов](./media/resource-group-move-resources/select-move.png)

Укажите, куда будут перемещены ресурсы: в новую группу ресурсов или новую подписку.

Выберите ресурсы, которые необходимо переместить, и целевую группу ресурсов. Подтвердите обновление сценариев для этих ресурсов и нажмите кнопку **ОК**. Если на предыдущем шаге был выбран значок редактирования подписки, то также необходимо выбрать целевую подписку.

![выбор места назначения](./media/resource-group-move-resources/select-destination.png)

В области **уведомлений**вы увидите, что операция перемещения выполняется.

![отображение состояния перемещения](./media/resource-group-move-resources/show-status.png)

После ее завершения отобразится уведомление о результате.

![отображение результата перемещения](./media/resource-group-move-resources/show-result.png)

Если отобразится сообщение об ошибке, см. в разделе [Устранение неполадок, перемещение ресурсов Azure в новую группу ресурсов или подписку](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Использование Azure PowerShell

Чтобы переместить существующие ресурсы в другую группу ресурсов или подписку, используйте команду [Move-AzResource](/powershell/module/az.resources/move-azresource). В следующем примере показано, как переместить несколько ресурсов в новую группу ресурсов.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Чтобы переместить ресурс в новую подписку, добавьте значение параметра `DestinationSubscriptionId`.

Если отобразится сообщение об ошибке, см. в разделе [Устранение неполадок, перемещение ресурсов Azure в новую группу ресурсов или подписку](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Использование Azure CLI

Чтобы переместить существующие ресурсы в другую группу или подписку, используйте команду [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move). Укажите идентификаторы перемещаемых ресурсов. В следующем примере показано, как переместить несколько ресурсов в новую группу ресурсов. В параметре `--ids` укажите перемещаемый список идентификаторов ресурсов с разделителями-пробелами.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Для перемещения в новую подписку нужно указать параметр `--destination-subscription-id`.

Если отобразится сообщение об ошибке, см. в разделе [Устранение неполадок, перемещение ресурсов Azure в новую группу ресурсов или подписку](troubleshoot-move.md).

## <a name="use-rest-api"></a>Использование REST API

Чтобы переместить существующие ресурсы в другую группу ресурсов или подписку, используйте [перемещение ресурсов](/rest/api/resources/Resources/MoveResources) операции.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

В тексте запроса укажите целевую группу ресурсов и ресурсы для перемещения.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Если отобразится сообщение об ошибке, см. в разделе [Устранение неполадок, перемещение ресурсов Azure в новую группу ресурсов или подписку](troubleshoot-move.md).

## <a name="next-steps"></a>Следующие шаги

Список, из которых ресурсы поддерживают перемещение, см. в разделе [перемещения операции благодаря поддержке ресурсов](move-support-resources.md).
