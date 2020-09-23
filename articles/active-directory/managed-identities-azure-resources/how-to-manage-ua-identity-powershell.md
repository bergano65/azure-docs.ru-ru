---
title: Создание, перечисление & удаление назначенного пользователем управляемого удостоверения с помощью Azure PowerShell Azure AD
description: Пошаговые инструкции по созданию, перечислению и удалению назначенного пользователем управляемого удостоверения с помощью Azure PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8649c9faf3905e69232cdc15bbba6607abe3e9c4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969515"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Создание, перечисление или удаление назначенного пользователем управляемого удостоверения с помощью Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Управляемые удостоверения для ресурсов Azure предоставляют службы Azure с управляемым удостоверением в Azure Active Directory. Это удостоверение можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure AD, без учетных данных в коде. 

Из этой статьи вы узнаете, как создать, перечислить и удалить назначенное пользователем управляемое удостоверение с помощью Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#managed-identity-types)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения примеров сценариев существует два варианта.
    - Используйте [Azure Cloud Shell](../../cloud-shell/overview.md), которую можно открыть с помощью кнопки **попробовать** в верхнем правом углу блоков кода.
    - Запускайте сценарии локально с Azure PowerShell, как описано в следующем разделе.

### <a name="configure-azure-powershell-locally"></a>Локальная настройка Azure PowerShell

Чтобы использовать Azure PowerShell локально для этой статьи (вместо использования Cloud Shell), выполните следующие действия.

1. Установите [последнюю версию Azure PowerShell](/powershell/azure/install-az-ps), если это еще не сделано.

1. Войдите в Azure.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Установите [PowerShellGet последней версии](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    `Exit`После выполнения этой команды для следующего шага может потребоваться выйти из текущего сеанса PowerShell.

1. Установите предварительную версию `Az.ManagedServiceIdentity` модуля, чтобы выполнить управляемые пользователем операции идентификации в этой статье:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем

Чтобы создать назначаемое пользователем управляемое удостоверение, учетной записи должна быть назначена роль [участника управляемого удостоверения](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Для создания управляемого удостоверения, назначаемого пользователем, используйте команду `New-AzUserAssignedIdentity`. Параметр `ResourceGroupName` указывает группу ресурсов, в которой создано управляемое удостоверение, назначаемое пользователем, а параметр `-Name` — его имя. Замените значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Получение списка управляемых удостоверений, назначаемых пользователем

Чтобы получить список управляемых удостоверений, назначаемых пользователем, или отобразить их, учетной записи должна быть назначена роль [оператора управляемого удостоверения](../../role-based-access-control/built-in-roles.md#managed-identity-operator) или [участника управляемого удостоверения](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Чтобы получить список управляемых удостоверений, назначаемых пользователем, выполните команду [Get-AzUserAssigned].  Параметр `-ResourceGroupName` указывает группу ресурсов, в которой было создано управляемое удостоверение, назначаемое пользователем. Замените `<RESOURCE GROUP>` собственным значением.

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
В ответе управляемые удостоверения, назначаемые пользователем, содержат значение `"Microsoft.ManagedIdentity/userAssignedIdentities"`, возвращаемое для ключа `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Удаление управляемого удостоверения, назначаемого пользователем

Чтобы удалить назначаемое пользователем управляемое удостоверение, учетной записи должна быть назначена роль [участника управляемого удостоверения](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Для удаления управляемого удостоверения, назначаемого пользователем, используйте команду `Remove-AzUserAssignedIdentity`.  Параметр `-ResourceGroupName` указывает группу ресурсов, в которой было создано удостоверение, назначаемое пользователем, а параметр `-Name` — его имя. Замените значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> При удалении управляемого удостоверения, назначаемого пользователем, не удаляются ссылки из ресурсов, которым оно было назначено. Назначения удостоверения должны быть удалены отдельно.

## <a name="next-steps"></a>Дальнейшие действия

Полный список команд Azure PowerShell для управляемых удостоверений для ресурсов Azure и дополнительные сведения о них приведены в разделе [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
