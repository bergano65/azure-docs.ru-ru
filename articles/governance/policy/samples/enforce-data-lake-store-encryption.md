---
title: 'Пример: требование шифрования для Data Lake Store'
description: В этом примере определения политики требуется включить состояние шифрования для учетных записей Data Lake Store.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 5b29caa7fa8b9169b8c2164975f9cdb2663c5cef
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463641"
---
# <a name="sample---require-data-lake-store-encryption"></a>Пример обязательного шифрования для Data Lake Store

Эта встроенная политика запрещает все учетные записи Data Lake Store, у которых не включено шифрование.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Пример шаблона

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DataLakeStore/accounts"
      },
      {
        "field": "Microsoft.DataLakeStore/accounts/encryptionState",
        "equals": "Disabled"
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Можно развернуть шаблон с помощью [портала Azure](#deploy-with-the-portal), [PowerShell](#deploy-with-powershell) или [интерфейса командной строки Azure](#deploy-with-azure-cli). Чтобы получить встроенную политику, используйте идентификатор `a7ff3161-0087-490a-9ad9-ad6217f4f43a`.

## <a name="deploy-with-the-portal"></a>Развертывание с помощью портала

При назначении политики выберите **Принудительное шифрование в учетных записях DataLakeStore** в списке доступных встроенных определений.

## <a name="deploy-with-powershell"></a>Развертывание с помощью PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a7ff3161-0087-490a-9ad9-ad6217f4f43a

New-AzPolicyAssignment -name "Data Lake Store encryption" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Очистка развертывания, выполненного с помощью PowerShell

Чтобы удалить назначение политики, выполните следующую команду:

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "Data Lake Store encryption" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "Data Lake Store encryption" --policy a7ff3161-0087-490a-9ad9-ad6217f4f43a
```

### <a name="clean-up-azure-cli-deployment"></a>Очистка развертывания, выполненного с помощью интерфейса командной строки Azure

Чтобы удалить назначение политики, выполните следующую команду:

```azurecli-interactive
az policy assignment delete --name "Data Lake Store encryption" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

- Другие примеры см. в статье [Примеры для Политики Azure](index.md).