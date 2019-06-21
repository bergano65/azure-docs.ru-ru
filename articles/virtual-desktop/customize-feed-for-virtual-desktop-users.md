---
title: Настройте канал для пользователей виртуальный рабочий стол Windows - Azure
description: Как настроить веб-канала для пользователей виртуальных рабочих столов Windows с помощью командлетов PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/30/2019
ms.author: v-hevem
ms.openlocfilehash: 869760a12089ed7453199ad8a3fa18a6cca87511
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157094"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Настройка канала для пользователей Виртуального рабочего стола Windows

Вы можете настроить веб-канала, образом запоминающееся способ для пользователей удаленного рабочего стола ресурсам и приложениям RemoteApp.

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) для использования в сеансе PowerShell (если вы еще это не сделали).

## <a name="customize-the-display-name-for-a-remoteapp"></a>Настройки отображаемого имени для RemoteApp

Отображаемое имя для опубликованных RemoteApp можно изменить, задав понятное имя. По умолчанию понятное имя совпадает с именем программы RemoteApp.

Чтобы получить список опубликованных RemoteApps для группы приложений, выполните следующий командлет PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Снимок экрана PowerShell командлет Get-RDSRemoteApp с именем и FriendlyName выделены.](media/get-rdsremoteapp.png)

Чтобы назначить понятное имя в удаленное приложение RemoteApp, выполните следующий командлет PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Снимок экрана PowerShell командлет Set-RDSRemoteApp с именем и новый FriendlyName выделены.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Настройки отображаемого имени для удаленного рабочего стола

Отображаемое имя для опубликованного удаленного рабочего стола можно изменить, задав понятное имя. Если вы вручную создали узле пула и группу настольных приложений с помощью PowerShell, понятное имя по умолчанию — «Рабочий стол сеанса». Если вы создали узле пула и группу настольных приложений с помощью шаблона диспетчера ресурсов Azure в GitHub или предложения Azure Marketplace, понятное имя по умолчанию — совпадает с именем узла пула.

Чтобы получить ресурс удаленного рабочего стола, выполните следующий командлет PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Снимок экрана PowerShell командлет Get-RDSRemoteApp с именем и FriendlyName выделены.](media/get-rdsremotedesktop.png)

Чтобы назначить понятное имя ресурса удаленного рабочего стола, выполните следующий командлет PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Снимок экрана PowerShell командлет Set-RDSRemoteApp с именем и новый FriendlyName выделены.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы настроили веб-канала для пользователей, вы можете войти в клиенту виртуального рабочего стола Windows для тестирования функции. Чтобы сделать это, перейдите к подключения виртуальный рабочий стол инструкции Windows:
    
 * [Подключение из Windows 10 или Windows 7](connect-windows-7-and-10.md)
 * [Подключение из веб-браузера](connect-web.md) 
