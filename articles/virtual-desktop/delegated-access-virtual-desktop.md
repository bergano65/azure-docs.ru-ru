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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82612866"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Делегированный доступ в Виртуальном рабочем столе Windows

>[!IMPORTANT]
>Это содержимое применимо к обновлению за весну 2020 года с объектами Azure Resource Manager для Виртуального рабочего стола Windows. Если вы используете выпуск Виртуального рабочего стола Windows за осень 2019 года без объектов Azure Resource Manager, см. [эту статью](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).
>
> Обновление Виртуального рабочего стола Windows за весну 2020 года пока предоставляется как общедоступная предварительная версия. без соглашений об уровне обслуживания. Мы не рекомендуем использовать ее для выполнения производственных рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
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
