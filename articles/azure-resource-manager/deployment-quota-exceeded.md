---
title: Превышена квота на развертывание Azure
description: В этой статье описывается, как устранить ошибку, которая содержит более 800 развертываний в журнале группы ресурсов.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: tomfitz
ms.openlocfilehash: 755383c9d40c104d50ad9bb7a31b3a00f8348313
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827014"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Устранять ошибку, если число развертываний превышает 800

Каждая группа ресурсов ограничена до 800 развертываний в журнале развертывания. В этой статье описывается ошибка, которая возникает при сбое развертывания из-за превышения допустимых развертываний 800. Чтобы устранить эту ошибку, удалите развертывания из журнала группы ресурсов. Удаление развертывания из журнала не влияет ни на один из развернутых ресурсов.

## <a name="symptom"></a>Симптом

Во время развертывания появляется сообщение об ошибке, сообщающее, что текущее развертывание превысит квоту на 800 развертываний.

## <a name="solution"></a>Решение

### <a name="azure-cli"></a>Azure CLI

Для удаления развертываний из журнала используйте команду [AZ Group Deployment Delete](/cli/azure/group/deployment#az-group-deployment-delete) .

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
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

Текущее число можно получить в журнале развертывания с помощью следующей команды:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Сторонние решения

Следующие внешние решения относятся к конкретным сценариям:

* [Azure Logic Apps и решения PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Расширение Аздевопс](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
