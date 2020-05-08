---
title: Тип назначения "персональный Рабочий стол для виртуальных рабочих столов Windows" — Azure
description: Настройка типа назначения для пула узлов личного рабочего стола Windows.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8451dc14a7ed42aa92f9adbd5ad050936949e302
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612424"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Настройка типа назначения пула узлов личного рабочего стола

>[!IMPORTANT]
>Это содержимое относится к обновлению пружины 2020 с Azure Resource Manager объектов виртуальных рабочих столов Windows. Если вы используете виртуальный рабочий стол Windows в выпуске 2019 без Azure Resource Manager объектов, см. [эту статью](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).
>
> Обновление 2020 виртуального рабочего стола Windows в настоящее время находится в общедоступной предварительной версии. Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем использовать ее для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Вы можете настроить тип назначения для пула узлов личного рабочего стола, чтобы настроить среду виртуальных рабочих столов Windows в соответствии с вашими потребностями. В этом разделе мы покажем, как настроить автоматическое или прямое назначение для пользователей.

>[!NOTE]
> Инструкции, приведенные в этой статье, применимы только к пулам узлов персональных рабочих столов, а не к пулам узлов, так как пользователи в пулах пулов узлов не назначены конкретным узлам сеансов.

## <a name="prerequisites"></a>Предварительные условия

В этой статье предполагается, что вы уже скачали и установили модуль PowerShell для виртуальных рабочих столов Windows. Если вы этого не сделали, следуйте инструкциям в разделе [Настройка модуля PowerShell](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Настройка автоматического назначения

Автоматическое назначение — это тип назначения по умолчанию для новых пулов узлов личного рабочего стола, созданных в среде виртуальных рабочих столов Windows. Автоматическое назначение пользователей не требует определенного узла сеансов.

Чтобы автоматически назначить пользователей, сначала назначьте им пул узлов личного рабочего стола, чтобы они могли видеть Рабочий стол в своем канале. Когда назначенный пользователь запускает рабочий стол в своем веб-канале, он запросит доступный узел сеансов, если они еще не подключены к пулу узлов, что завершает процесс назначения.

Чтобы настроить пул узлов для автоматического назначения пользователей виртуальным машинам, выполните следующий командлет PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Чтобы назначить пользователя пулу узлов личного рабочего стола, выполните следующий командлет PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Настройка прямого назначения

В отличие от автоматического назначения, при использовании прямого назначения необходимо назначить пользователю пул узлов личного рабочего стола и конкретный узел сеанса, прежде чем они смогут подключиться к своему личному рабочему столу. Если пользователь назначен только пулу узлов без назначения узла сеансов, он не сможет получить доступ к ресурсам.

Чтобы настроить пул узлов так, чтобы требовать непосредственного назначения пользователей для узлов сеансов, выполните следующий командлет PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Чтобы назначить пользователя пулу узлов личного рабочего стола, выполните следующий командлет PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Чтобы назначить пользователя определенному узлу сеансов, выполните следующий командлет PowerShell:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы настроили тип назначения личный рабочий стол, вы можете войти в клиент виртуальных рабочих столов Windows, чтобы протестировать его в рамках сеанса пользователя. Эти две следующие инструкции помогут вам подключиться к сеансу с помощью выбранного клиента:

- [Подключение к клиенту Windows Desktop](connect-windows-7-and-10.md)
- [Подключение к веб-клиенту](connect-web.md)
- [Подключение к клиенту Android](connect-android.md)
- [Подключение с помощью клиента iOS](connect-ios.md)
- [Подключение с помощью клиента macOS](connect-macos.md)