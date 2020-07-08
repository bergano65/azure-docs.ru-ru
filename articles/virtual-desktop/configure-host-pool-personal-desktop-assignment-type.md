---
title: Тип назначения персонального рабочего стола "виртуальный рабочий стол Windows" — Azure
description: Как настроить тип назначения для пула узлов персонального рабочего стола "виртуальный рабочий стол Windows".
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158ac92a930b53e02ee81570c62711ca27dc4ae8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200398"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Настройка типа назначения пула узлов персонального рабочего стола

>[!IMPORTANT]
>Это содержимое применимо к обновлению за весну 2020 года с объектами Azure Resource Manager для Виртуального рабочего стола Windows. Если вы используете выпуск Виртуального рабочего стола Windows за осень 2019 года без объектов Azure Resource Manager, см. [эту статью](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).
>
> Обновление Виртуального рабочего стола Windows за весну 2020 года пока предоставляется как общедоступная предварительная версия. без соглашений об уровне обслуживания. Мы не рекомендуем использовать ее для выполнения производственных рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Можно настроить тип назначения пула узлов персонального рабочего стола так, чтобы корректировать среду виртуальных рабочих столов Windows в точном соответствии с вашими потребностями. В этом разделе мы покажем вам, как настроить автоматическое или прямое назначение для ваших пользователей.

>[!NOTE]
> Инструкции в этой статье применимы только к пулам узлов персональных рабочих столов, а не к общим пулам узлов, поскольку пользователи в общих пулах узлов не назначены никаким определенным узлам сеансов.

## <a name="prerequisites"></a>Предварительные условия

В этой статье предполагается, что вы уже скачали и установили модуль PowerShell для виртуальных рабочих столов Windows. Если вы этого не сделали, следуйте инструкциям в разделе [Настройка модуля PowerShell](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Настройка автоматического назначения

Автоматическое назначение — это тип назначения по умолчанию для новых пулов узлов персональных рабочих столов, Созданных в среде виртуальных рабочих столов Windows. Автоматическое назначение пользователей не требует определенного узла сеансов.

Для автоматического назначения пользователей сначала назначьте их пулу узлов персональных рабочих столов, чтобы они могли видеть рабочий стол в своем канале. Когда назначенный пользователь запускает рабочий стол в своем канале, он запрашивает доступный узел сеанса, если еще не подключился к пулу узлов, который завершает процесс назначения.

Чтобы настроить пул узлов для автоматического назначения пользователей виртуальным машинам, выполните следующий командлет PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Чтобы назначить пользователя пулу узлов виртуального рабочего стола, выполните следующий командлет PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Настройка прямого назначения

В отличие от автоматического назначения в этом случае необходимо назначать пользователя и пулу узлов персональных рабочих столов, и узлу конкретного сеанса — только после этого они смогут подключиться к персональному рабочему столу. Если пользователь назначен только пулу узлов, но не узлу сеансов, он не сможет осуществлять доступ к ресурсам.

Чтобы настроить требование пулом узлов прямого назначения пользователей узлам сеансов, выполните следующий командлет PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Чтобы назначить пользователя пулу узлов виртуального рабочего стола, выполните следующий командлет PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Чтобы назначить пользователя конкретному узлу сеансов, выполните следующий командлет PowerShell:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы настроили тип назначения персонального рабочего стола, можно войти в клиент виртуальных рабочих столов Windows, чтобы протестировать его в рамках пользовательского сеанса. В следующих двух практических статьях рассказывается, как подключиться к сеансу с помощью выбранного клиента:

- [Подключение с использованием клиента классических приложений Windows](connect-windows-7-and-10.md)
- [Подключение с помощью веб-клиента](connect-web.md)
- [Подключение к клиенту Android](connect-android.md)
- [Подключение с помощью клиента iOS](connect-ios.md)
- [Подключение с помощью клиента macOS](connect-macos.md)