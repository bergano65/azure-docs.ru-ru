---
title: Делегированный доступ в виртуальном рабочем столе Windows — Azure
description: Как делегировать административные возможности развертыванию виртуальных рабочих столов Windows, включая примеры.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4e63ec864da9a6e7ce5448b322e0452503e2493c
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853438"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Делегированный доступ в Виртуальном рабочем столе Windows

>[!IMPORTANT]
>Это содержимое применимо к Виртуальному рабочему столу Windows с объектами Azure Resource Manager для Виртуального рабочего стола Windows. Если вы используете Виртуальный рабочий стол Windows (классический) без объектов Azure Resource Manager, ознакомьтесь с [этой статьей](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).

Виртуальный рабочий стол Windows имеет делегированную модель доступа, которая позволяет определить объем доступа, который может иметь конкретный пользователь, назначив ему роль. Назначение ролей включает три компонента: субъект безопасности, определение роли и область. Модель делегированного доступа виртуальных рабочих столов Windows основана на модели RBAC Azure. Дополнительные сведения о конкретных назначениях ролей и их компонентах см. [в статье Общие сведения об управлении доступом на основе ролей в Azure](../role-based-access-control/built-in-roles.md).

Делегированный доступ к виртуальным рабочим столам Windows поддерживает следующие значения для каждого элемента назначения роли:

* Субъект безопасности
    * Пользователи
    * Группы пользователей
    * Субъекты-службы
* Определение роли
    * Встроенные роли
    * Пользовательские роли
* Область
    * Пулы узлов
    * Группы приложений
    * Рабочие области

## <a name="powershell-cmdlets-for-role-assignments"></a>Командлеты PowerShell для назначений ролей

Прежде чем начать, обязательно следуйте инструкциям в разделе [Настройка модуля PowerShell](powershell-module.md) , чтобы настроить модуль PowerShell для виртуальных рабочих столов Windows, если вы еще этого не сделали.

Виртуальный рабочий стол Windows использует управление доступом на основе ролей Azure (Azure RBAC) при публикации групп приложений для пользователей или групп пользователей. Роль пользователя виртуализации рабочих столов назначается пользователю или группе пользователей, а областью действия является группа приложений. Эта роль предоставляет пользователю специальные права доступа к данным в группе приложений.  

Выполните следующий командлет, чтобы добавить Azure Active Directory пользователей в группу приложений:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'  
```

Выполните следующий командлет, чтобы добавить Azure Active Directory группу пользователей в группу приложений:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

## <a name="next-steps"></a>Дальнейшие шаги

Более полный список командлетов PowerShell, которые может использовать каждая роль, см. в [справочнике по PowerShell](/powershell/windows-virtual-desktop/overview).

Полный список ролей, поддерживаемых в Azure RBAC, см. в статье [встроенные роли Azure](../role-based-access-control/built-in-roles.md).

Рекомендации по настройке среды виртуальных рабочих столов Windows см. в разделе [Среда виртуальных рабочих столов Windows](environment-setup.md).
