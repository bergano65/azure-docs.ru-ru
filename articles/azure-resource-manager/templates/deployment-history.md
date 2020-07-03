---
title: Журнал развертывания
description: Сведения о просмотре операций развертывания Azure Resource Manager с помощью портала, PowerShell, Azure CLI и REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: b0f196f86bed05094b04bfc20c7cef2248a91c65
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460302"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Просмотр журнала развертывания с помощью Azure Resource Manager

Azure Resource Manager позволяет просматривать журнал развертывания и проверять конкретные операции в прошлых развертываниях. Можно просмотреть развернутые ресурсы и получить сведения об ошибках.

Сведения об устранении некоторых ошибок развертывания см. в статье об [устранении распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Получение развертываний и идентификатора корреляции

Сведения о развертывании можно просмотреть с помощью портал Azure, PowerShell, Azure CLI или REST API. Каждое развертывание имеет идентификатор корреляции, который используется для наблюдения за связанными событиями. Это может быть полезно при работе с технической поддержкой для устранения неполадок при развертывании.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Выберите группу ресурсов, которую необходимо проверить.

1. Выберите ссылку в разделе **развертывания**.

   ![Выбор журнала развертывания](./media/deployment-history/select-deployment-history.png)

1. Выберите одно из развертываний из журнала развертывания.

   ![Выбор развертывания](./media/deployment-history/select-details.png)

1. Отобразится сводка развертывания, включая идентификатор корреляции.

    ![Сводка по развертыванию](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы получить список всех развертываний для группы ресурсов, используйте команду [Get-азресаурцеграупдеплоймент](/powershell/module/az.resources/Get-AzResourceGroupDeployment) .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Чтобы получить конкретное развертывание из группы ресурсов, добавьте параметр **DeploymentName** .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Чтобы получить идентификатор корреляции, используйте:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы получить список развертывания для группы ресурсов, используйте команду [AZ Deployment Group List](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Чтобы получить конкретное развертывание, используйте команду [AZ Deployment Group показывать](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Чтобы получить идентификатор корреляции, используйте:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Чтобы получить список развертываний для группы ресурсов, используйте следующую операцию. Последний номер версии API, используемый в запросе, см. в разделе [развертывания — список по группам ресурсов](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Для получения конкретного развертывания. Используйте следующую операцию. Последний номер версии API, используемый в запросе, см. в разделе [deployments-Get](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Ответ содержит идентификатор корреляции.

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

## <a name="get-deployment-operations-and-error-message"></a>Получить операции развертывания и сообщение об ошибке

Каждое развертывание может включать несколько операций. Чтобы просмотреть дополнительные сведения о развертывании, просмотрите операции развертывания. При сбое развертывания операции развертывания включают в себя сообщение об ошибке.

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. На странице Сводка по развертыванию выберите **сведения об операции**.

    ![Выбор операций развертывания](./media/deployment-history/get-operation-details.png)

1. Вы увидите сведения об этом шаге развертывания. При возникновении ошибки сведения включают в себя сообщение об ошибке.

    ![Отобразить сведения об операции](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы просмотреть операции развертывания для развертывания в группе ресурсов, используйте команду [Get-азресаурцеграупдеплойментоператион](/powershell/module/az.resources/get-azdeploymentoperation) .

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Чтобы просмотреть операции с ошибками, отфильтруйте операции с **неудачным** состоянием.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Чтобы получить сообщение о состоянии неудачных операций, используйте следующую команду:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы просмотреть операции развертывания для развертывания в группе ресурсов, используйте команду [AZ Deployment Group Operation List](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-deployment-group-operation-list) .

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeployment
```

Чтобы просмотреть операции с ошибками, отфильтруйте операции с **неудачным** состоянием.

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Чтобы получить сообщение о состоянии неудачных операций, используйте следующую команду:

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Чтобы получить операции развертывания, используйте следующую операцию. Последний номер версии API, используемый в запросе, см. в разделе [операции развертывания — список](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Ответ содержит сообщение об ошибке.

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

## <a name="next-steps"></a>Дальнейшие шаги

* Сведения об устранении некоторых ошибок развертывания см. в статье об [устранении распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](common-deployment-errors.md).
* Дополнительные сведения об использовании журналов действий для мониторинга других типов действий см. в статье [Операции аудита с помощью диспетчера ресурсов](../management/view-activity-logs.md).
* Чтобы проверить развернутую службу перед ее выполнением, ознакомьтесь со статьей [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](deploy-powershell.md).

