---
title: 'Пример: допустимые номера SKU для учетных записей хранения и виртуальных машин'
description: В этом примере определения политики требуется, чтобы учетные записи хранения и виртуальные машины использовали утвержденные номера SKU.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 088dd8414c93b609326e7cd72828426dcb00fc28
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274575"
---
# <a name="sample---allowed-skus-for-storage-accounts-and-virtual-machines"></a>Пример: допустимые номера SKU для учетных записей хранения и виртуальных машин

Эта политика требует, чтобы учетные записи хранения и виртуальные машины использовали утвержденные номера SKU. Для обеспечения утвержденных номеров SKU используются встроенные политики. Вы можете указать массив утвержденных номеров SKU виртуальных машин и учетных записей хранения.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Пример шаблона

[!code-json[main](../../../../policy-templates/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.json "Allowed SKUs for Storage Accounts and Virtual Machines")]

Этот шаблон можно развернуть с помощью [портала Azure](#deploy-with-the-portal) или [PowerShell](#deploy-with-powershell).

## <a name="deploy-with-the-portal"></a>Развертывание с помощью портала

[![DРазвертывание примера политики в Azure](https://azuredeploy.net/deploybutton.png)](https://aka.ms/getpolicy)

## <a name="deploy-with-powershell"></a>Развертывание с помощью PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.parameters.json"

$policyset= New-AzPolicySetDefinition -Name "skus-for-multiple-types" -DisplayName "Allowed SKUs for Storage Accounts and Virtual Machines" -Description "This policy allows you to speficy what skus are allowed for storage accounts and virtual machines" -PolicyDefinition $policydefinitions -Parameter $policysetparameters 
 
New-AzPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentName> -Scope <scope>  -LISTOFALLOWEDSKUS_1 <VM SKUs> -LISTOFALLOWEDSKUS_2 <Storage Account SKUs>
```

### <a name="clean-up-powershell-deployment"></a>Очистка развертывания, выполненного с помощью PowerShell

Чтобы удалить назначение и определение политики, выполните следующую команду.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name <assignmentName>
Remove-AzPolicySetDefinitions -Name "skus-for-multiple-types"
```

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy set-definition create --name "skus-for-multiple-types" --display-name "Allowed SKUs for Storage Accounts and Virtual Machines" --description "This policy allows you to speficy what skus are allowed for storage accounts and virtual machines" --definitions "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.definitions.json" --params "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.parameters.json"

az policy assignment create --name <assignmentName> --scope <scope> --policy-set-definition "skus-for-multiple-types" --params "{ 'LISTOFALLOWEDSKUS_1': { 'value': <VM SKU Array> }, 'LISTOFALLOWEDSKUS_2': { 'value': <Storage Account SKU Array> } }"
```

### <a name="clean-up-azure-cli-deployment"></a>Очистка развертывания, выполненного с помощью интерфейса командной строки Azure

Чтобы удалить назначение и определение политики, выполните следующую команду.

```azurecli-interactive
az policy assignment delete --name <assignmentName>
az policy set-definition delete --name "skus-for-multiple-types"
```

## <a name="next-steps"></a>Дополнительная информация

- Другие примеры см. в статье [Примеры для Политики Azure](index.md).