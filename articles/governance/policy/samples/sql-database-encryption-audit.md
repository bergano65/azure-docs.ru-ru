---
title: 'Пример: аудит состояния прозрачного шифрования данных для базы данных SQL'
description: В этом примере политика проверяет, включено ли прозрачное шифрование данных для базы данных SQL.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 10/29/2018
ms.author: dacoulte
ms.openlocfilehash: 36eb62dfa7ae45b70f5907241c66f2286f9ce760
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310629"
---
# <a name="audit-sql-database-encryption"></a>Проверка шифрования базы данных SQL

Эта встроенная политика проверяет, включено ли прозрачное шифрование данных для базы данных SQL.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Пример шаблона

[!code-json[main](../../../../policy-templates/samples/SQL/audit-sql-db-tde-status/azurepolicy.json "Audit TDE for SQL Database")]

Можно развернуть шаблон с помощью [портала Azure](#deploy-with-the-portal), [PowerShell](#deploy-with-powershell) или [интерфейса командной строки Azure](#deploy-with-azure-cli). Чтобы получить встроенную политику, используйте идентификатор `17k78e20-9358-41c9-923c-fb736d382a12`.

## <a name="deploy-with-the-portal"></a>Развертывание с помощью портала

При назначении политики выберите **Проверка состояния прозрачного шифрования данных** в списке доступных встроенных определений.

## <a name="deploy-with-powershell"></a>Развертывание с помощью PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzureRmPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Очистка развертывания, выполненного с помощью PowerShell

Чтобы удалить назначение политики, выполните следующую команду:

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>Очистка развертывания, выполненного с помощью интерфейса командной строки Azure

Чтобы удалить назначение политики, выполните следующую команду:

```azurecli-interactive
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

- Другие примеры см. в статье [Примеры для Политики Azure](index.md).