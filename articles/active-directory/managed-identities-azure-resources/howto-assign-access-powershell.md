---
title: Как назначить доступ на основе управляемого удостоверения к ресурсу Azure с помощью PowerShell
description: Пошаговые инструкции по назначению управляемого удостоверения для одного ресурса и предоставлению доступа к другому ресурсу с помощью PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff36be7f87d0dd9e5cac5ee7f788eec0cda5a9fd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60290704"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Назначение доступа на основе управляемого удостоверения к ресурсу с помощью PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

После настройки ресурса Azure с помощью управляемого удостоверения можно предоставить доступ на основе управляемого удостоверения другому ресурсу, как и любому субъекту безопасности. В этом примере показано, как предоставить управляемому удостоверению виртуальной машине Azure доступ к учетной записи хранения Azure с помощью PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)** .
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Установите [последнюю версию Azure PowerShell](/powershell/azure/install-az-ps), если это еще не сделано.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Назначение управляемому удостоверению прав доступа к другому ресурсу с помощью RBAC

После включения управляемого удостоверения для ресурса Azure, [например виртуальной машины Azure](qs-configure-powershell-windows-vm.md), сделайте следующее.

1. Войдите в Azure с помощью командлета `Connect-AzAccount`. Используйте учетную запись, связанную с подпиской Azure, с помощью которой было настроено управляемое удостоверение.

   ```powershell
   Connect-AzAccount
   ```
2. В этом примере мы предоставляем виртуальной машине Azure доступ к учетной записи хранения. Сначала с помощью [Get-AzVM](/powershell/module/az.compute/get-azvm) получите для виртуальной машины с именем `myVM` субъект-службу, который был создан при включении управляемого удостоверения. Затем с помощью [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) предоставьте этой виртуальной машине доступ для **чтения** к учетной записи хранения с именем `myStorageAcct`.

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Дальнейшие действия

- [Что такое управляемые удостоверения для ресурсов Azure?](overview.md)
- Дополнительные сведения см. в статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью PowerShell](qs-configure-powershell-windows-vm.md).
