---
title: Настройка веб-канала для пользователей виртуальных рабочих столов Windows в Azure
description: Настройка веб-канала для пользователей виртуальных рабочих столов Windows с помощью командлетов PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 49b678bea820f3cbead7479bb414ca9f35a29fa4
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163619"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Настройка канала для пользователей Виртуального рабочего стола Windows

Вы можете настроить веб-канал таким образом, чтобы ресурсы RemoteApp и удаленный рабочий стол отображались для пользователей с распознаваемым способом.

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) для использования в сеансе PowerShell (если вы еще это не сделали). После этого выполните следующий командлет, чтобы войти в учетную запись:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Настройка отображаемого имени для RemoteApp

Вы можете изменить отображаемое имя для опубликованного RemoteApp, задав понятное имя. По умолчанию понятное имя совпадает с именем удаленного приложения RemoteApp.

Чтобы получить список опубликованных RemoteApp для группы приложений, выполните следующий командлет PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Снимок экрана командлета PowerShell Get-Рдсремотеапп с выделенным именем и FriendlyName.](media/get-rdsremoteapp.png)

Чтобы назначить понятное имя RemoteApp, выполните следующий командлет PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Снимок экрана командлета PowerShell Set-Рдсремотеапп с именем и новым FriendlyName.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Настройка отображаемого имени для удаленный рабочий стол

Вы можете изменить отображаемое имя для опубликованного удаленного рабочего стола, указав Понятное имя. Если вы вручную создали пул узлов и группу классических приложений с помощью PowerShell, понятное имя по умолчанию — "сеанс рабочего стола". Если вы создали пул узлов и группу классических приложений с помощью шаблона Azure Resource Manager GitHub или предложения Azure Marketplace, понятное имя по умолчанию совпадает с именем пула узлов.

Чтобы получить ресурс удаленного рабочего стола, выполните следующий командлет PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Снимок экрана командлета PowerShell Get-Рдсремотеапп с выделенным именем и FriendlyName.](media/get-rdsremotedesktop.png)

Чтобы назначить понятное имя ресурсу удаленного рабочего стола, выполните следующий командлет PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Снимок экрана командлета PowerShell Set-Рдсремотеапп с именем и новым FriendlyName.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы настроили веб-канал для пользователей, вы можете войти в клиент виртуальных рабочих столов Windows, чтобы протестировать его. Для этого перейдите к инструкциям по подключению к виртуальному рабочему столу Windows.
    
 * [Подключение из Windows 10 или Windows 7](connect-windows-7-and-10.md)
 * [Подключение из веб-браузера](connect-web.md) 
