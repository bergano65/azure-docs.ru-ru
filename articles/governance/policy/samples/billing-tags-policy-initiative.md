---
title: 'Пример: инициатива политики тегов для выставления счетов'
description: В этом примере определения политики требуется указать значения тега для обозначения места возникновения затрат и имени продукта.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: f2190b5759c53d645c1d0150004271ba04669c94
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981399"
---
# <a name="sample---billing-tags-policy-initiative"></a>Пример: инициатива политики тегов для выставления счетов

Эта политика требует указать значения тега для обозначения места возникновения затрат и имени продукта. Используются встроенные политики для применения и принудительного назначения требуемых тегов. Вы можете указать необходимые значения тегов.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Пример шаблона

[!code-json[main](../../../../policy-templates/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.json "Billing Tags Policy Initiative")]

Вы можете развернуть этот шаблон с помощью [PowerShell](#deploy-with-powershell).

## <a name="deploy-with-powershell"></a>Развертывание с помощью PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.parameters.json"

$policyset= New-AzPolicySetDefinition -Name "multiple-billing-tags" -DisplayName "Billing Tags Policy Initiative" -Description "Specify cost Center tag and product name tag" -PolicyDefinition $policydefinitions -Parameter $policysetparameters

New-AzPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentname> -Scope <scope>  -costCenterValue <required value for Cost Center tag> -productNameValue <required value for product Name tag>
```

### <a name="clean-up-powershell-deployment"></a>Очистка развертывания, выполненного с помощью PowerShell

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="apply-tags-to-existing-resources"></a>Применение тегов к существующим ресурсам

После назначения политик можно активировать обновление для всех существующих ресурсов, чтобы применить добавленные политики тегов. Следующий сценарий сохраняет другие теги, которые существовали в ресурсах:

```azurepowershell-interactive
$resources = Get-AzResource -ResourceGroupName 'ExampleGroup'

foreach ($r in $resources) {
    try {
        Set-AzResource -Tags ($a = if ($r.Tags -eq $NULL) { @{} } else {$r.Tags}) -ResourceId $r.ResourceId -Force -UsePatchSemantics
    }
    catch {
        Write-Host $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

- Другие примеры см. в статье [Примеры для Политики Azure](index.md).