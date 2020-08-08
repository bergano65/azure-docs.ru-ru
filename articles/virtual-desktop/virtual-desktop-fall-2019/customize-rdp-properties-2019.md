---
title: Настройка свойств RDP с помощью PowerShell Windows Virtual Desktop (классическая модель) — Azure
description: Настройка свойств RDP для виртуальных рабочих столов Windows (классическая модель) с помощью командлетов PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1df576e3316a529ec38c4a53960ff817d129ffd8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002262"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>Настройка свойств протокол удаленного рабочего стола для пула узлов виртуальных рабочих столов Windows (классическая модель)

>[!IMPORTANT]
>Это содержимое применимо к Виртуальному рабочему столу Windows (классическому), который не поддерживает объекты Azure Resource Manager для Виртуального рабочего стола Windows. Сведения об обеспечении управления объектами Azure Resource Manager для Виртуального рабочего стола Windows см. в [этой статье](../customize-rdp-properties.md).

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
| Режим с несколькими мониторами | Включено | Н/Д |
| Перенаправление дисков включено | Диски, буфер обмена, принтеры, COM-порты, USB-устройства и SmartCards| Диски, буфер обмена и принтеры |
| Режим удаленного звука | Воспроизвести локально | Воспроизвести локально |

Все пользовательские свойства, определяемые для пула узлов, будут переопределять эти значения по умолчанию.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Добавление или изменение одного настраиваемого свойства RDP

Чтобы добавить или изменить одно настраиваемое свойство RDP, выполните следующий командлет PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![Снимок экрана командлета PowerShell Get-Рдсремотеапп с выделенным именем и FriendlyName.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Добавление или изменение нескольких настраиваемых свойств RDP

Чтобы добавить или изменить несколько настраиваемых свойств RDP, выполните следующие командлеты PowerShell, предоставив настраиваемые свойства RDP в виде строки с разделителями-запятыми:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![Снимок экрана командлета PowerShell Get-Рдсремотеапп с выделенным именем и FriendlyName.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Сброс всех настраиваемых свойств RDP

Можно восстановить значения по умолчанию для отдельных настраиваемых свойств RDP, следуя инструкциям в разделе [Добавление или изменение одного настраиваемого](#add-or-edit-a-single-custom-rdp-property)свойства RDP или сброс всех настраиваемых свойств RDP для пула узлов, выполнив следующий командлет PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![Снимок экрана командлета PowerShell Get-Рдсремотеапп с выделенным именем и FriendlyName.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы настроили свойства протокола удаленного рабочего стола для определенного пула узлов, вы можете войти в клиент виртуальных рабочих столов Windows, чтобы протестировать их в рамках сеанса пользователя. В следующих двух практических статьях рассказывается, как подключиться к сеансу с помощью выбранного клиента:

- [Подключение с использованием клиента классических приложений Windows](connect-windows-7-10-2019.md)
- [Подключение с помощью веб-клиента](connect-web-2019.md)
