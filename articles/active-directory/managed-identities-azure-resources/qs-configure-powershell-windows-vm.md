---
title: Настройка управляемых удостоверений на виртуальной машине Azure с помощью PowerShell — Azure AD
description: Пошаговые инструкции по настройке управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью Azure.
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
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f24c89477d71df3f497590b49841403576343bd4
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547213"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как с помощью PowerShell выполнять приведенные ниже операции с управляемыми удостоверениями для ресурсов Azure на виртуальной машине Azure.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Установите [последнюю версию Azure PowerShell](/powershell/azure/install-az-ps), если это еще не сделано.

## <a name="system-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое системой

В этом разделе вы узнаете, как включить и отключить управляемое удостоверение, назначаемое системой, с помощью Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Включение управляемого удостоверения, назначаемого системой, во время создания виртуальной машины Azure

Чтобы создать виртуальную машину Azure с включенным управляемым удостоверением, назначаемым системой, вашей учетной записи должна быть назначена роль [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Назначение других ролей в каталоге Azure AD не требуется.

1. Ознакомьтесь с одним из следующих кратких руководств по виртуальным машинам Azure, заполнив только необходимые разделы ("войти в Azure", "создать группу ресурсов", "создать сетевую группу", "создать виртуальную машину").
    
    При выполнении действий, описанных в разделе о создании виртуальной машины, не забудьте внести небольшие изменения в синтаксис командлета [New-AzVMConfig](/powershell/module/az.compute/new-azvm). Чтобы подготовить виртуальную машину с включенным удостоверением, назначаемым системой, добавьте параметр `-AssignIdentity:$SystemAssigned`, как показано ниже.
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Создание виртуальной машины Windows с помощью PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Создание виртуальной машины Linux с помощью PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Включение управляемого удостоверения, назначаемого системой, для имеющейся виртуальной машины Azure

Чтобы включить назначаемое системой управляемое удостоверение на виртуальной машине, которая была подготовлена без него, вашей учетной записи должна быть назначена роль [Участника виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Назначение других ролей в каталоге Azure AD не требуется.

1. Войдите в Azure, используя команду `Connect-AzAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину.

   ```powershell
   Connect-AzAccount
   ```

2. Сначала извлеките свойства виртуальной машины с помощью командлета `Get-AzVM`. Затем, чтобы включить управляемое удостоверение, назначаемое системой, используйте параметр `-AssignIdentity` в командлете [Update-AzVM](/powershell/module/az.compute/update-azvm).

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Добавление назначаемого системой удостоверения виртуальной машины в группу

После включения назначенного системной удостоверения на виртуальной машине, вы можете добавить его в группу.  Следующая процедура добавляет назначенное системой удостоверение в группу.

1. Войдите в Azure, используя команду `Connect-AzAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину.

   ```powershell
   Connect-AzAccount
   ```

2. Получите и запишите `ObjectID` (как указано в поле `Id` возвращаемых значений) субъекта-службы виртуальной машины.

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Получите и запишите `ObjectID` (как указано в поле `Id` возвращаемых значений) группы.

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Добавление субъекта-службы виртуальной машины в группу.

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Отключение управляемого удостоверения, назначаемого системой, на виртуальной машине Azure

Чтобы отключить назначаемое системой управляемое удостоверение на виртуальной машине, вашей учетной записи должна быть назначена роль [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Назначение других ролей в каталоге Azure AD не требуется.

Если на виртуальной машине больше не требуется управляемое удостоверение, назначаемое системой, но по-прежнему требуются управляемые удостоверения, назначаемые пользователем, используйте следующий командлет.

1. Войдите в Azure, используя команду `Connect-AzAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину.

   ```powershell
   Connect-AzAccount
   ```

2. Получите свойства виртуальной машины с помощью командлета `Get-AzVM` и задайте для параметра `-IdentityType` значение `UserAssigned`.

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Если имеется виртуальная машина, для которой не требуется управляемое удостоверение, назначаемое системой, и у которой нет управляемых удостоверений, назначаемых пользователем, используйте следующие команды.

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое пользователем

В этом разделе вы узнаете, как добавить и удалить управляемое удостоверение, назначаемое пользователем, для виртуальной машины с помощью Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Задание управляемого удостоверения, назначаемого пользователем, для виртуальной машины во время ее создания

Чтобы присвоить назначаемое пользователем удостоверение виртуальной машине, вашей учетной записи должны быть назначены роли [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) и [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Назначение других ролей в каталоге Azure AD не требуется.

1. Ознакомьтесь с одним из следующих кратких руководств по виртуальным машинам Azure, заполнив только необходимые разделы ("войти в Azure", "создать группу ресурсов", "создать сетевую группу", "создать виртуальную машину"). 
  
    При выполнении действий, описанных в разделе о создании виртуальной машины, не забудьте внести небольшие изменения в синтаксис командлета [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm). Добавьте параметры `-IdentityType UserAssigned` и `-IdentityID`, чтобы подготовить виртуальную машину с удостоверением, назначаемым пользователем.  Замените `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.  Пример.
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Создание виртуальной машины Windows с помощью PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Создание виртуальной машины Linux с помощью PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Задание управляемого удостоверения, назначаемого пользователем, для имеющейся виртуальной машины Azure

Чтобы присвоить назначаемое пользователем удостоверение виртуальной машине, вашей учетной записи должны быть назначены роли [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) и [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Назначение других ролей в каталоге Azure AD не требуется.

1. Войдите в Azure, используя команду `Connect-AzAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину.

   ```powershell
   Connect-AzAccount
   ```

2. Создайте управляемое удостоверение, назначаемое пользователем, с помощью командлета [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity).  Обратите внимание на свойство `Id` в выходных данных. Оно потребуется в следующем шаге.

   > [!IMPORTANT]
   > Создание управляемых удостоверений, назначаемых пользователем, поддерживает только буквы, цифры, символы подчеркивания и дефис (0-9 или a-z или A-Z, \_ или-). Кроме того, имя должно быть ограничено длиной от 3 до 128 символов, чтобы назначение виртуальной машине или VMSS работало правильно. Дополнительные сведения см. в разделе [Часто задаваемые вопросы и известные проблемы](known-issues.md).

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Извлеките свойства виртуальной машины с помощью командлета `Get-AzVM`. Затем, чтобы задать управляемое удостоверение, назначаемое пользователем, для виртуальной машины Azure, используйте параметры `-IdentityType` и `-IdentityID` в командлете [Update-AzVM](/powershell/module/az.compute/update-azvm).  Для параметра `-IdentityId` следует использовать значение `Id` из предыдущего шага.  Замените `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.

   > [!WARNING]
   > Чтобы сохранить ранее назначенные пользователем управляемые удостоверения, предназначенные для виртуальной машины, запросите свойство `Identity` объекта VM (например, `$vm.Identity`).  Если какие-либо назначенные пользователем управляемые удостоверения возвращаются, включите их в следующую команду вместе с новым удостоверением, которое необходимо назначить виртуальной машине.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Удаление управляемого удостоверения, назначаемого пользователем, из виртуальной машины Azure

Чтобы удалить назначаемое пользователем удостоверение на виртуальной машине, вашей учетной записи должна быть назначена роль [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).

Если у виртуальной машины несколько управляемых удостоверений, назначаемых пользователем, с помощью приведенных ниже команд можно удалить все эти удостоверения, кроме последнего. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VM NAME>` собственными. `<USER ASSIGNED IDENTITY NAME>` — это имя управляемого удостоверения, назначаемого пользователем, которое следует оставить в виртуальной машине. Эти сведения можно найти, запросив свойство `Identity` объекта виртуальной машины.  Например, `$vm.Identity`:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Если в виртуальной машине нет управляемого удостоверения, назначаемого системой, и вы хотите удалить из нее все управляемые удостоверения, назначаемые пользователем, используйте следующую команду.

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Если у виртуальной машины есть управляемые удостоверения, назначаемые системой и назначаемые пользователем, вы можете удалить все управляемые удостоверения, назначаемые пользователем, переключившись на использование только управляемого удостоверения, назначаемого системой.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Дальнейшие действия

- [Обзор управляемых удостоверений для ресурсов Azure](overview.md).
- Ниже приведены комплексные краткие руководства по созданию виртуальных машин Azure:
  
  - [Создание виртуальной машины Windows с помощью PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Создание виртуальной машины Linux с помощью PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
