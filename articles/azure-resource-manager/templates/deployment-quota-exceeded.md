---
title: Превышена квота развертывания
description: В этой статье описывается, как устранить ошибку, которая содержит более 800 развертываний в журнале группы ресурсов.
ms.topic: troubleshooting
ms.date: 06/25/2020
ms.openlocfilehash: 1b0c3de6007964b487a13e71cd43bd984cd970f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391185"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Устранять ошибку, если число развертываний превышает 800

Каждая группа ресурсов ограничена до 800 развертываний в журнале развертывания. В этой статье описывается ошибка, которая возникает при сбое развертывания из-за превышения допустимых развертываний 800. Чтобы устранить эту ошибку, удалите развертывания из журнала группы ресурсов. Удаление развертывания из журнала не влияет ни на один из развернутых ресурсов.

> [!NOTE]
> Azure Resource Manager скоро начнет автоматически удалять развертывания из журнала, так как вы приближаетесь к ограничению. Эта ошибка может по-прежнему отображаться, если вы отказались от автоматического удаления. Дополнительные сведения см. [в разделе Автоматическое удаление из журнала развертывания](deployment-history-deletions.md).

## <a name="symptom"></a>Симптом

Во время развертывания появляется сообщение об ошибке, сообщающее, что текущее развертывание превысит квоту на 800 развертываний.

## <a name="solution"></a>Решение

### <a name="azure-cli"></a>Azure CLI

Чтобы удалить развертывания из журнала, используйте команду [AZ Deployment Group Delete](/cli/azure/group/deployment) .

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

Текущее число можно получить в журнале развертывания с помощью следующей команды:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
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
