---
title: Превышена квота на развертывание Azure
description: В этой статье описывается, как устранить ошибку, которая содержит более 800 развертываний в журнале группы ресурсов.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.author: tomfitz
ms.openlocfilehash: 7f9e1b6b8518ebc03f051e379e4707dd1864e003
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73578972"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Устранять ошибку, если число развертываний превышает 800

Каждая группа ресурсов ограничена до 800 развертываний в журнале развертывания. В этой статье описывается ошибка, которая возникает при сбое развертывания из-за превышения допустимых развертываний 800. Чтобы устранить эту ошибку, удалите развертывания из журнала группы ресурсов. Удаление развертывания из журнала не влияет ни на один из развернутых ресурсов.

## <a name="symptom"></a>Симптом

Во время развертывания появляется сообщение об ошибке, сообщающее, что текущее развертывание превысит квоту на 800 развертываний.

## <a name="solution"></a>Решение

### <a name="azure-cli"></a>Azure CLI

Для удаления развертываний из журнала используйте команду [AZ Group Deployment Delete](/cli/azure/group/deployment#az-group-deployment-delete) .

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

Чтобы удалить все развертывания старше пяти дней, используйте:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az group deployment list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az group deployment delete --resource-group exampleGroup --name $deployment
done
```

Текущее число можно получить в журнале развертывания с помощью следующей команды:

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Чтобы удалить развертывания из журнала, используйте команду [Remove-азресаурцеграупдеплоймент](/powershell/module/az.resources/remove-azresourcegroupdeployment) .

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

Текущее число можно получить в журнале развертывания с помощью следующей команды:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Сторонние решения

Следующие внешние решения относятся к конкретным сценариям:

* [Azure Logic Apps и решения PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Расширение Аздевопс](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
