---
title: 'Пример: разрешение на использование нескольких шаблонов имен'
description: В этом примере определения политики требуется, чтобы ресурс соответствовал одной из цифр указанных шаблонов имен.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 227b03d0719fcf1a8851213d96b057c9b439a99d
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463478"
---
# <a name="sample---allow-multiple-name-patterns"></a>Пример разрешения на использование нескольких шаблонов имен

Разрешение на применение одного из нескольких шаблонов имен для ресурсов. Укажите допустимые шаблоны в правиле политики.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Пример шаблона

[!code-json[main](../../../../policy-templates/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.json "allow multiple name patterns")]

Можно развернуть шаблон с помощью [портала Azure](#deploy-with-the-portal), [PowerShell](#deploy-with-powershell) или [интерфейса командной строки Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Развертывание с помощью портала

[![Развертывание примера политики в Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FTextPatterns%2Fallow-multiple-name-patterns%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Развертывание с помощью PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "allow-multiple-name-patterns" -DisplayName "Allow one of many name patterns to be used for resources." -description "Allow one of many name patterns to be used for resources." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Очистка развертывания, выполненного с помощью PowerShell

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'allow-multiple-name-patterns' --display-name 'Allow one of many name patterns to be used for resources.' --description 'Allow one of many name patterns to be used for resources.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "allow-multiple-name-patterns" 
```

### <a name="clean-up-azure-cli-deployment"></a>Очистка развертывания, выполненного с помощью интерфейса командной строки Azure

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Дополнительная информация

- Другие примеры см. в статье [Примеры для Политики Azure](index.md).