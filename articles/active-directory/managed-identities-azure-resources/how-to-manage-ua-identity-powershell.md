---
title: Создание, перечисление & удаление назначенного пользователем управляемого удостоверения с помощью Azure PowerShell Azure AD
description: Пошаговые инструкции по созданию и удалению управляемых удостоверений, назначаемых пользователем, а также получения их списка с помощью Azure PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3c4005643de55720864da1e87d8bea82b21749c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232248"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Создание и удаление управляемых удостоверений, назначаемых пользователем, а также получение их списка с помощью Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure AD, без учетных данных в коде. 

В этой статье вы узнаете, как создавать и удалять управляемые удостоверения, назначаемые пользователем, а также получить их список с помощью Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>предварительным требованиям

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)** .
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Установите [последнюю версию Azure PowerShell](/powershell/azure/install-az-ps), если это еще не сделано.
- Если модуль PowerShell запущен локально, необходимо также выполнить следующие действия. 
    - Выполните команду `Connect-AzAccount`, чтобы создать подключение к Azure.
    - Установите [PowerShellGet последней версии](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Выполните `Install-Module -Name PowerShellGet -AllowPrerelease`, чтобы получить предварительную версию модуля `PowerShellGet` (может потребоваться `Exit` из текущего сеанса PowerShell после выполнения этой команды для установки модуля `Az.ManagedServiceIdentity`).
    - В этой статье необходимо выполнить `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease`, чтобы установить предварительную версию модуля `Az.ManagedServiceIdentity` для выполнения операций с управляемыми удостоверениями, назначаемыми пользователем.

## <a name="create-a-user-assigned-managed-identity"></a>Создание назначаемого пользователем управляемого удостоверения.

Чтобы создать назначаемое пользователем управляемое удостоверение, учетной записи должна быть назначена роль [участника управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Для создания управляемого удостоверения, назначаемого пользователем, используйте команду `New-AzUserAssignedIdentity`. Параметр `ResourceGroupName` указывает группу ресурсов, в которой создано управляемое удостоверение, назначаемое пользователем, а параметр `-Name` — его имя. Замените значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Получение списка управляемых удостоверений, назначаемых пользователем

Чтобы получить список управляемых удостоверений, назначаемых пользователем, или отобразить их, учетной записи должна быть назначена роль [оператора управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator) или [участника управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Чтобы получить список управляемых удостоверений, назначаемых пользователем, выполните команду [Get-AzUserAssigned].  Параметр `-ResourceGroupName` указывает группу ресурсов, в которой было создано управляемое удостоверение, назначаемое пользователем. Замените `<RESOURCE GROUP>` собственным значением.

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
В ответе управляемые удостоверения, назначаемые пользователем, содержат значение `"Microsoft.ManagedIdentity/userAssignedIdentities"`, возвращаемое для ключа `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Удаление управляемого удостоверения, назначаемого пользователем

Чтобы удалить назначаемое пользователем управляемое удостоверение, учетной записи должна быть назначена роль [Участника управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Для удаления управляемого удостоверения, назначаемого пользователем, используйте команду `Remove-AzUserAssignedIdentity`.  Параметр `-ResourceGroupName` указывает группу ресурсов, в которой было создано удостоверение, назначаемое пользователем, а параметр `-Name` — его имя. Замените значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> При удалении управляемого удостоверения, назначаемого пользователем, не удаляются ссылки из ресурсов, которым оно было назначено. Назначения удостоверения должны быть удалены отдельно.

## <a name="next-steps"></a>Дополнительная информация

Полный список команд Azure PowerShell для управляемых удостоверений для ресурсов Azure и дополнительные сведения о них приведены в разделе [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
