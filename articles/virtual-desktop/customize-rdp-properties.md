---
title: Настройте свойства RDP с помощью PowerShell - Azure
description: Как настроить RDP Свойства для Windows Виртуальный рабочий стол с PowerShell cmdlets.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128059"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Настройка свойств удаленного протокола рабочего стола для пула хоста

Настройка свойств удаленного настольного протокола пула (RDP), таких как многомониторный опыт и перенаправление звука, позволяет обеспечить оптимальный опыт для пользователей в зависимости от их потребностей. Вы можете настроить свойства RDP в Windows Virtual Desktop с помощью параметра **-CustomRdpProperty** в cmdlet **Set-RdsHostPool.**

Смотрите [поддерживаемые настройки файлов RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) для полного списка поддерживаемых свойств и значений их значения по умолчанию.

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](/powershell/windows-virtual-desktop/overview/) для использования в сеансе PowerShell (если вы еще это не сделали). После этого выполните следующий командлет, чтобы войти в учетную запись:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Свойства RDP по умолчанию

По умолчанию опубликованные файлы RDP содержат следующие свойства:

|Свойства RDP | Настольные системы | RemoteApps |
|---|---| --- |
| Режим мультимонитора | Активировано | Недоступно |
| Включено перенаправление привода | Диски, буфер обмена, принтеры, порты COM, USB-устройства и смарт-карты| Диски, буфер обмена и принтеры |
| Удаленный аудиорежим | Играть локально | Играть локально |

Любые пользовательские свойства, определяемые для пула хоста, переопределяют эти значения по умолчанию.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Добавление или отодевание одного пользовательского свойства RDP

Чтобы добавить или отсваивать одно пользовательское свойство RDP, запустите следующее cmdlet PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Скриншот PowerShell cmdlet Get-RDSRemoteApp с именем и FriendlyName выделены.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Добавление или отодевочное несколько пользовательских свойств RDP

Чтобы добавить или отсваивать несколько пользовательских свойств RDP, запустите следующие cmdlets PowerShell, предоставив пользовательские свойства RDP в виде строки, разделенной за счет запятой:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Скриншот PowerShell cmdlet Get-RDSRemoteApp с именем и FriendlyName выделены.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Сбросить все пользовательские свойства RDP

Вы можете сбросить индивидуальные пользовательские свойства RDP на их значения по умолчанию, следуя инструкциям в [Добавлении или отредкобестом одного пользовательского свойства RDP,](#add-or-edit-a-single-custom-rdp-property)или вы можете сбросить все пользовательские свойства RDP для пула хоста, запустив следующие значения PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Скриншот PowerShell cmdlet Get-RDSRemoteApp с именем и FriendlyName выделены.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы настроили свойства RDP для данного пула хостов, вы можете войти в клиент Windows Virtual Desktop, чтобы протестировать их как часть сеанса пользователя. Следующие два How-tos расскажут вам, как подключиться к сеансу с помощью клиента по вашему выбору:

- [Подключение к клиенту Windows Desktop](connect-windows-7-and-10.md)
- [Подключение к веб-клиенту](connect-web.md)
