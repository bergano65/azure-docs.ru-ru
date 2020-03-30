---
title: Настройка корма для пользователей виртуального рабочего стола Windows - Azure
description: Как настроить канал для пользователей виртуального рабочего стола Windows с помощью cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128082"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Настройка канала для пользователей Виртуального рабочего стола Windows

Вы можете настроить канал, чтобы удаленные приложения и удаленные настольные ресурсы отображались в узнаваемым образом для ваших пользователей.

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](/powershell/windows-virtual-desktop/overview/) для использования в сеансе PowerShell (если вы еще это не сделали). После этого выполните следующий командлет, чтобы войти в учетную запись:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Настройка имени дисплея для Удаленного Приложения

Вы можете изменить название дисплея для опубликованного RemoteApp, установив дружественное имя. По умолчанию, дружественное имя такое же, как и название программы RemoteApp.

Чтобы получить список опубликованных Удаленных Приложений для группы приложений, запустите следующее cmdlet PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Скриншот PowerShell cmdlet Get-RDSRemoteApp с именем и FriendlyName выделены.](media/get-rdsremoteapp.png)

Чтобы присвоить дружественное имя RemoteApp, запустите следующее cmdlet PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Скриншот PowerShell cmdlet Set-RDSRemoteApp с именем и New FriendlyName выделены.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Настройка имени дисплея для удаленного рабочего стола

Вы можете изменить имя дисплея для опубликованного удаленного рабочего стола, установив дружественное имя. Если вы вручную создали пул хоста и группу настольных приложений через PowerShell, то дружественным по умолчанию названием является "Session Desktop". Если вы создали группу пула хоста и настольного приложения через шаблон GitHub Azure Resource Manager или предложение Azure Marketplace, имя, дружественное по умолчанию, совпадает с именем пула.

Чтобы получить ресурс удаленного рабочего стола, запустите следующее cmdlet PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Скриншот PowerShell cmdlet Get-RDSRemoteApp с именем и FriendlyName выделены.](media/get-rdsremotedesktop.png)

Чтобы присвоить дружественное имя ресурсу удаленного рабочего стола, запустите следующее cmdlet PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Скриншот PowerShell cmdlet Set-RDSRemoteApp с именем и New FriendlyName выделены.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы настроили канал для пользователей, вы можете войти в Windows Virtual Desktop клиента, чтобы проверить его. Чтобы сделать это, продолжить подключение к Windows Виртуальный настольный Как-tos:
    
 * [Подключение из Windows 10 или Windows 7](connect-windows-7-and-10.md)
 * [Подключение из веб-браузера](connect-web.md) 
