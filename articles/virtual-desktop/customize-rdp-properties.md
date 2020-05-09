---
title: Настройка свойств RDP с помощью PowerShell — Azure
description: Настройка свойств RDP для виртуальных рабочих столов Windows с помощью командлетов PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 66b76fcdd9729b2a92ea2d561c740dbe148e0bbe
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611557"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Настройка свойств протокол удаленного рабочего стола пула узлов

>[!IMPORTANT]
>Это содержимое относится к обновлению пружины 2020 с Azure Resource Manager объектов виртуальных рабочих столов Windows. Если вы используете виртуальный рабочий стол Windows в выпуске 2019 без Azure Resource Manager объектов, см. [эту статью](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).
>
> Обновление 2020 виртуального рабочего стола Windows в настоящее время находится в общедоступной предварительной версии. Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем использовать ее для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Настройка свойств протокол удаленного рабочего стола пула узлов (RDP), таких как использование нескольких мониторов и перенаправление звука, позволяет обеспечить оптимальную работу пользователей в зависимости от их потребностей. Свойства RDP можно настроить в виртуальном рабочем столе Windows с помощью портал Azure или с помощью параметра *-кустомрдппроперти* в командлете **Update-азввдхостпул** .

Полный список поддерживаемых свойств и их значений по умолчанию см. в разделе [Поддерживаемые параметры RDP-файла](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .

## <a name="prerequisites"></a>Предварительные условия

Прежде чем начать, следуйте инструкциям в разделе [Настройка модуля PowerShell для виртуальных рабочих столов Windows](powershell-module.md) , чтобы настроить модуль PowerShell и войти в Azure.

## <a name="default-rdp-properties"></a>Свойства RDP по умолчанию

По умолчанию опубликованные RDP-файлы содержат следующие свойства.

|Свойства RDP | Настольные системы | RemoteApps |
|---|---| --- |
| Режим с несколькими мониторами | Активировано | Н/Д |
| Перенаправление дисков включено | Диски, буфер обмена, принтеры, COM-порты, USB-устройства и SmartCards| Диски, буфер обмена и принтеры |
| Режим удаленного звука | Воспроизвести локально | Воспроизвести локально |

Все пользовательские свойства, определяемые для пула узлов, будут переопределять эти значения по умолчанию.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Настройка свойств RDP в портал Azure

Чтобы настроить свойства RDP в портал Azure:

1. Войдите в Azure по адресу <https://portal.azure.com>.
2. В строке поиска введите **Windows Virtual Desktop** .
3. В разделе "службы" выберите **Windows виртуальный рабочий стол**.
4. На странице виртуальных рабочих столов Windows в меню в левой части экрана выберите **Пулы узлов** .
5. Выберите **имя пула узлов** , который требуется обновить.
6. Выберите **Свойства** в меню в левой части экрана.
7. Выберите **параметры RDP** , чтобы начать редактирование свойств RDP.
8. Когда все будет готово, нажмите кнопку **сохранить** , чтобы сохранить изменения.

Если требуется изменить параметр, который не отображается в меню параметры RDP, необходимо изменить его вручную, запустив командлеты в PowerShell. В следующих разделах вы узнаете, как изменить настраиваемые свойства RDP вручную в PowerShell.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Добавление или изменение одного настраиваемого свойства RDP

Чтобы добавить или изменить одно настраиваемое свойство RDP, выполните следующий командлет PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

Чтобы проверить, обновлялся ли только что запущенный командлет, выполните следующий командлет:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Например, если вы проводили проверку свойства "аудиокаптуремоде" в пуле узлов с именем 0301HP, необходимо ввести следующий командлет:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Добавление или изменение нескольких настраиваемых свойств RDP

Чтобы добавить или изменить несколько настраиваемых свойств RDP, выполните следующие командлеты PowerShell, предоставив настраиваемые свойства RDP в виде строки с разделителями-запятыми:

```powershell
$properties="<property1>;<property2>;<property3>" 
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties 
```

Чтобы проверить, было ли Добавлено свойство RDP, выполните следующий командлет:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty 

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Если вы настроили несколько свойств RDP в пуле узлов 0301HP, то в соответствии с предыдущим примером командлет будет выглядеть следующим образом:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty 

Name              : 0301HP 
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Сброс всех настраиваемых свойств RDP

Можно восстановить значения по умолчанию для отдельных настраиваемых свойств RDP, следуя инструкциям в разделе [Добавление или изменение одного настраиваемого](#add-or-edit-a-single-custom-rdp-property)свойства RDP или сброс всех настраиваемых свойств RDP для пула узлов, выполнив следующий командлет PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

Чтобы убедиться, что этот параметр успешно удален, введите следующий командлет:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty 

Name              : <hostpoolname> 
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы настроили свойства протокола удаленного рабочего стола для определенного пула узлов, вы можете войти в клиент виртуальных рабочих столов Windows, чтобы протестировать их в рамках сеанса пользователя. В следующих руководствах вы узнаете, как подключиться к сеансу с помощью выбранного клиента:

- [Подключение к клиенту Windows Desktop](connect-windows-7-and-10.md)
- [Подключение к веб-клиенту](connect-web.md)
- [Подключение к клиенту Android](connect-android.md)
- [Подключение с помощью клиента macOS](connect-macos.md)
- [Подключение с помощью клиента iOS](connect-ios.md)