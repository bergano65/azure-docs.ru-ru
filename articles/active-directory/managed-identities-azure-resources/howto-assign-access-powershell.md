---
title: Как назначить доступ на основе управляемого удостоверения к ресурсу Azure с помощью PowerShell
description: Пошаговые инструкции по назначению управляемого удостоверения для одного ресурса и предоставлению доступа к другому ресурсу с помощью PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: daveba
ms.openlocfilehash: 7ed61ead475acb81da4434c880954e801492351b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081563"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Назначение доступа на основе управляемого удостоверения к ресурсу с помощью PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

После настройки ресурса Azure с помощью управляемого удостоверения можно предоставить доступ на основе управляемого удостоверения другому ресурсу, как и любому субъекту безопасности. В этом примере показано, как предоставить управляемому удостоверению виртуальной машине Azure доступ к учетной записи хранения Azure с помощью PowerShell.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Установите [последнюю версию Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), если это еще не сделано.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Назначение управляемому удостоверению прав доступа к другому ресурсу с помощью RBAC

После включения управляемого удостоверения для ресурса Azure, [например виртуальной машины Azure](qs-configure-powershell-windows-vm.md), сделайте следующее.

1. Войдите в Azure с помощью командлета `Connect-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, с помощью которой было настроено управляемое удостоверение.

   ```powershell
   Connect-AzureRmAccount
   ```
2. В этом примере мы предоставляем виртуальной машине Azure доступ к учетной записи хранения. Сначала с помощью [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) получите субъект-службу для виртуальной машины с именем `myVM`, который был создан при включении управляемого удостоверения. Затем с помощью [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) предоставьте этой виртуальной машине доступ для **чтения** к учетной записи хранения с именем `myStorageAcct`.

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Дополнительная информация

- [Что такое управляемые удостоверения для ресурсов Azure?](overview.md)
- Дополнительные сведения см. в статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью PowerShell](qs-configure-powershell-windows-vm.md).
