---
title: Настройка свойств RDP с помощью PowerShell — Azure
description: Настройка свойств RDP для виртуальных рабочих столов Windows с помощью командлетов PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128059"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Настройка свойств протокол удаленного рабочего стола пула узлов

Настройка свойств протокол удаленного рабочего стола пула узлов (RDP), таких как использование нескольких мониторов и перенаправление звука, позволяет обеспечить оптимальную работу пользователей в зависимости от их потребностей. Свойства RDP можно настроить в виртуальном рабочем столе Windows с помощью параметра **-кустомрдппроперти** в командлете **Set-рдшостпул** .

Полный список поддерживаемых свойств и их значений по умолчанию см. в разделе [Поддерживаемые параметры RDP-файла](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](/powershell/windows-virtual-desktop/overview/) для использования в сеансе PowerShell (если вы еще это не сделали). После этого выполните следующий командлет, чтобы войти в учетную запись:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Свойства RDP по умолчанию

По умолчанию опубликованные RDP-файлы содержат следующие свойства.

|Свойства RDP | Настольные системы | RemoteApps |
|---|---| --- |
| Режим с несколькими мониторами | Активировано | Недоступно |
| Перенаправление дисков включено | Диски, буфер обмена, принтеры, COM-порты, USB-устройства и SmartCards| Диски, буфер обмена и принтеры |
| Режим удаленного звука | Воспроизвести локально | Воспроизвести локально |

Все пользовательские свойства, определяемые для пула узлов, будут переопределять эти значения по умолчанию.

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

## <a name="next-steps"></a>Дальнейшие шаги

Теперь, когда вы настроили свойства протокола удаленного рабочего стола для определенного пула узлов, вы можете войти в клиент виртуальных рабочих столов Windows, чтобы протестировать их в рамках сеанса пользователя. Эти две следующие инструкции помогут вам подключиться к сеансу с помощью выбранного клиента:

- [Подключение к клиенту Windows Desktop](connect-windows-7-and-10.md)
- [Подключение к веб-клиенту](connect-web.md)
