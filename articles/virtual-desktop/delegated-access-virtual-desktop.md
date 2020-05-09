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
ms.openlocfilehash: 16b4fca475f91a8cb5b7f9a20ea5aa74b6b674a3
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612866"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Делегированный доступ в Виртуальном рабочем столе Windows

>[!IMPORTANT]
>Это содержимое относится к обновлению пружины 2020 с Azure Resource Manager объектов виртуальных рабочих столов Windows. Если вы используете виртуальный рабочий стол Windows в выпуске 2019 без Azure Resource Manager объектов, см. [эту статью](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).
>
> Обновление 2020 виртуального рабочего стола Windows в настоящее время находится в общедоступной предварительной версии. Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем использовать ее для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Виртуальный рабочий стол Windows использует управление доступом на основе ролей (RBAC) Azure при публикации групп приложений для пользователей или групп пользователей. Роль пользователя виртуализации рабочих столов назначается пользователю или группе пользователей, а областью действия является группа приложений. Эта роль предоставляет пользователю специальные права доступа к данным в группе приложений.  

Выполните следующий командлет, чтобы добавить Azure Active Directory пользователей в группу приложений:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'  
```

Выполните следующий командлет, чтобы добавить Azure Active Directory группу пользователей в группу приложений:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

## <a name="next-steps"></a>Дальнейшие действия

Более полный список командлетов PowerShell, которые может использовать каждая роль, см. в [справочнике по PowerShell](/powershell/windows-virtual-desktop/overview).

Полный список ролей, поддерживаемых в Azure RBAC, см. в статье [встроенные роли Azure](../role-based-access-control/built-in-roles.md).

Рекомендации по настройке среды виртуальных рабочих столов Windows см. в разделе [Среда виртуальных рабочих столов Windows](environment-setup.md).
