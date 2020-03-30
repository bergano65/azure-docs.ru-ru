---
title: Квота развертывания превышена
description: Описывает, как устранить ошибку, связанную с наличием более 800 развертываний в истории группы ресурсов.
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 919cd9a3482401cd47516e2677b0bf58387488b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245095"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Устранение ошибки при превышении количества развертывания

Каждая группа ресурсов ограничена 800 развертываниями в истории развертывания. В этой статье описывается ошибка, которую вы получаете при сходе развертывания из-за того, что она превысит допустимые 800 развертываний. Чтобы устранить эту ошибку, удалите развертывания из истории группы ресурсов. Исечение развертывания из истории не влияет ни на один из ресурсов, которые были развернуты.

## <a name="symptom"></a>Симптом

Во время развертывания вы получаете ошибку, в ходе которого указано, что текущее развертывание превысит квоту в 800 развертываний.

## <a name="solution"></a>Решение

### <a name="azure-cli"></a>Azure CLI

Используйте команду [удаления группы развертывания аз](/cli/azure/group/deployment) для удаления развертываний из истории.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Чтобы удалить все развертывания старше пяти дней, используйте:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

Текущий подсчет можно получить в истории развертывания со следующей командой:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Используйте команду [Удалить-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) для удаления развертываний из истории.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Чтобы удалить все развертывания старше пяти дней, используйте:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Текущий подсчет можно получить в истории развертывания со следующей командой:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Сторонние решения

Следующие внешние решения касаются конкретных сценариев:

* [Логические приложения Azure и решения PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Расширение AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
