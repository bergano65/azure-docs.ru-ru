---
title: Тип назначения личного рабочего стола Windows Virtual Desktop - Azure
description: Как настроить тип назначения для пула персональных настольных компьютеров Windows Virtual Desktop.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128288"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Настройка типа назначения пула пула рабочего стола

Вы можете настроить тип назначения вашего личного пула настольного хоста, чтобы настроить среду Windows Virtual Desktop, чтобы лучше соответствовать вашим потребностям. В этой теме мы покажем вам, как настроить автоматическое или прямое назначение для ваших пользователей.

>[!NOTE]
> Инструкции в этой статье распространяются только на пулы персональных узлов, а не на пулы хостов, поскольку пользователи в объединенных пулах узлов не назначаются определенным хостам сеанса.

## <a name="configure-automatic-assignment"></a>Настройка автоматического назначения

Автоматическое назначение — это тип назначения по умолчанию для новых персональных пулов настольных компьютеров, созданных в среде Windows Virtual Desktop. Автоматическое назначение пользователей не требует конкретного хоста.

Чтобы автоматически назначать пользователей, сначала назначайте их в личный пул настольного хоста, чтобы они могли видеть рабочий стол в своей ленте. Когда назначенный пользователь запускает рабочий стол в своем канале, он будет требовать доступный хост сеанса, если он еще не подключен к пулу узлов, который завершает процесс назначения.

Перед тем, как начать, [загрузите и импортируйте модуль Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) если вы еще не сделали этого. 

> [!NOTE]
> Убедитесь, что вы установили Windows Virtual Desktop PowerShell модуль версии 1.0.1534.2001 или позже, прежде чем следовать этим инструкциям.

После этого выполните следующий командлет, чтобы войти в учетную запись:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Чтобы настроить пул хоста для автоматического присвоения пользователям VMs, запустите следующий cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Чтобы назначить пользователя в личный пул настольного хоста, запустите следующий cmdlet PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Настройка прямого назначения

В отличие от автоматического назначения, при использовании прямого назначения необходимо назначить пользователя как в личный пул настольного хоста, так и на конкретный хост сеанса, прежде чем он сможет подключиться к своему личному рабочему столу. Если пользователь назначен только пулу хоста без назначения узла сеанса, он не сможет получить доступ к ресурсам.

Чтобы настроить пул хоста, требующий прямого назначения пользователей хостам сеанса, запустите следующее cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Чтобы назначить пользователя в личный пул настольного хоста, запустите следующий cmdlet PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Чтобы назначить пользователя определенному хостам сеанса, запустите следующий cmdlet PowerShell:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы настроили персональный тип назначения рабочего стола, вы можете войти в клиент Windows Virtual Desktop, чтобы протестировать его как часть сеанса пользователя. Следующие два How-tos расскажут вам, как подключиться к сеансу с помощью клиента по вашему выбору:

- [Подключение к клиенту Windows Desktop](connect-windows-7-and-10.md)
- [Подключение к веб-клиенту](connect-web.md)
