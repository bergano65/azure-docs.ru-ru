---
title: Журнал развертывания
description: Сведения о просмотре операций развертывания Azure Resource Manager с помощью портала, PowerShell, Azure CLI и REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: b0f196f86bed05094b04bfc20c7cef2248a91c65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460302"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Просмотр истории развертывания с помощью менеджера ресурсов Azure

Диспетчер ресурсов Azure позволяет просматривать историю развертывания и изучать конкретные операции в прошлых развертываниях. Вы можете увидеть ресурсы, которые были развернуты, и получить информацию о любых ошибках.

Сведения об устранении некоторых ошибок развертывания см. в статье об [устранении распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Получить развертывание и идентификатор корреляции

Вы можете просматривать сведения о развертывании через портал Azure, PowerShell, Azure CLI или REST API. Каждое развертывание имеет идентификатор корреляции, который используется для отслеживания связанных событий. Это может быть полезно при работе с технической поддержкой для устранения неполадок в развертывании.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Выберите группу ресурсов, которые вы хотите изучить.

1. Выберите ссылку под **развертыванием.**

   ![Выберите историю развертывания](./media/deployment-history/select-deployment-history.png)

1. Выберите одно из развертываний из истории развертывания.

   ![Выбор развертывания](./media/deployment-history/select-details.png)

1. Отображается резюме развертывания, включая идентификатор корреляции.

    ![Сводка по развертыванию](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы перечислить все развертывания для группы ресурсов, используйте команду [Get-AzResourceGroupDeployment.](/powershell/module/az.resources/Get-AzResourceGroupDeployment)

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Чтобы получить определенное развертывание из группы ресурсов, добавьте параметр **DeploymentName.**

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Чтобы получить идентификатор корреляции, используйте:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Чтобы перечислить развертывание для группы ресурсов, используйте [список групп развертывания az.](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-list)

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Чтобы получить конкретное развертывание, используйте [шоу группы развертывания az.](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-show)

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Чтобы получить идентификатор корреляции, используйте:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Чтобы перечислить развертывания для группы ресурсов, используйте следующую операцию. Для последнего номера версии API, [Deployments - List By Resource Group](/rest/api/resources/deployments/listbyresourcegroup)который можно использовать в запросе, см.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Для получения конкретного развертывания. использовать следующую операцию. Для последнего номера версии API, [Deployments - Get](/rest/api/resources/deployments/get)который можно использовать в запросе, см.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Ответ включает идентификатор корреляции.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Получайте операции развертывания и сообщение об ошибке

Каждое развертывание может включать в себя несколько операций. Чтобы узнать больше о развертывании, просмотрите операции развертывания. При сходе развертывания в эксплуатацию в операции развертывания содержится сообщение об ошибке.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. В резюме для развертывания выберите **детали операции.**

    ![Выберите операции развертывания](./media/deployment-history/get-operation-details.png)

1. Вы видите детали для этого шага развертывания. При возникновении ошибки в детали входит сообщение об ошибке.

    ![Отображить детали операции](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Для просмотра операций развертывания для развертывания в группе ресурсов используйте команду [Get-AzResourceGroupDeploymentOperation.](/powershell/module/az.resources/get-azdeploymentoperation)

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Для просмотра сбой операций фильтрует операции с **неудавшимся** состоянием.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Чтобы получить сообщение о состоянии неудачных операций, используйте следующую команду:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Для просмотра операций развертывания для развертывания в группе ресурсов используйте команду [списка операций группы аз.](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-deployment-group-operation-list)

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeployment
```

Для просмотра сбой операций фильтрует операции с **неудавшимся** состоянием.

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Чтобы получить сообщение о состоянии неудачных операций, используйте следующую команду:

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Для выполнения операций развертывания используйте следующую операцию. Для последнего номера версии API, [Deployment Operations - List](/rest/api/resources/deploymentoperations/list)который можно использовать в запросе, см.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Ответ включает сообщение об ошибке.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об устранении некоторых ошибок развертывания см. в статье об [устранении распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](common-deployment-errors.md).
* Дополнительные сведения об использовании журналов действий для мониторинга других типов действий см. в статье [Операции аудита с помощью диспетчера ресурсов](../management/view-activity-logs.md).
* Чтобы проверить развернутую службу перед ее выполнением, ознакомьтесь со статьей [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](deploy-powershell.md).

