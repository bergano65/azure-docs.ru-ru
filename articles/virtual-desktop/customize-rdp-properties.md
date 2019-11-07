---
title: Настройка свойств RDP с помощью PowerShell — Azure
description: Настройка свойств RDP для виртуальных рабочих столов Windows с помощью командлетов PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 8679f5dcd01a6cc660561097fc607fabdfdbba71
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607014"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Настройка свойств протокол удаленного рабочего стола пула узлов

Настройка свойств протокол удаленного рабочего стола пула узлов (RDP), таких как использование нескольких мониторов и перенаправление звука, позволяет обеспечить оптимальную работу пользователей в зависимости от их потребностей. Свойства RDP можно настроить в виртуальном рабочем столе Windows с помощью параметра **-кустомрдппроперти** в командлете **Set-рдшостпул** .

Полный список поддерживаемых свойств и их значений по умолчанию см. в разделе [параметры удаленный рабочий стол RDP-файла](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) .

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) для использования в сеансе PowerShell (если вы еще это не сделали). После этого выполните следующий командлет, чтобы войти в учетную запись:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Добавление или изменение одного настраиваемого свойства RDP

Чтобы добавить или изменить одно настраиваемое свойство RDP, выполните следующий командлет PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Снимок экрана командлета PowerShell Get-Рдсремотеапп с выделенным именем и FriendlyName.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Добавление или изменение нескольких настраиваемых свойств RDP

Чтобы добавить или изменить несколько настраиваемых свойств RDP, выполните следующие командлеты PowerShell, предоставив настраиваемые свойства RDP в виде строки с разделителями-запятыми:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Снимок экрана командлета PowerShell Get-Рдсремотеапп с выделенным именем и FriendlyName.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Сброс всех настраиваемых свойств RDP

Можно восстановить значения по умолчанию для отдельных настраиваемых свойств RDP, следуя инструкциям в разделе [Добавление или изменение одного настраиваемого](#add-or-edit-a-single-custom-rdp-property)свойства RDP или сброс всех настраиваемых свойств RDP для пула узлов, выполнив следующий командлет PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Снимок экрана командлета PowerShell Get-Рдсремотеапп с выделенным именем и FriendlyName.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы настроили свойства протокола удаленного рабочего стола для определенного пула узлов, вы можете войти в клиент виртуальных рабочих столов Windows, чтобы протестировать их в рамках сеанса пользователя. Для этого перейдите к инструкциям по подключению к виртуальному рабочему столу Windows.

- [Подключение из Windows 10 и Windows 7](connect-windows-7-and-10.md)
- [Подключение из веб-браузера](connect-web.md)
