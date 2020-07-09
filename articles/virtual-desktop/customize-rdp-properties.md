---
title: Настройка свойств RDP с помощью PowerShell — Azure
description: Настройка свойств RDP для виртуальных рабочих столов Windows с помощью командлетов PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4cbfe03c48c6d971c140dc332c38a47bc9234173
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135111"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Настройка свойств протокол удаленного рабочего стола пула узлов

>[!IMPORTANT]
>Это содержимое применимо к обновлению за весну 2020 года с объектами Azure Resource Manager для Виртуального рабочего стола Windows. Если вы используете выпуск Виртуального рабочего стола Windows за осень 2019 года без объектов Azure Resource Manager, см. [эту статью](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).
>
> Обновление Виртуального рабочего стола Windows за весну 2020 года пока предоставляется как общедоступная предварительная версия. без соглашений об уровне обслуживания. Мы не рекомендуем использовать ее для выполнения производственных рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Настройка свойств протокол удаленного рабочего стола пула узлов (RDP), таких как использование нескольких мониторов и перенаправление звука, позволяет обеспечить оптимальную работу пользователей в зависимости от их потребностей. Свойства RDP можно настроить в виртуальном рабочем столе Windows с помощью портал Azure или с помощью параметра *-кустомрдппроперти* в командлете **Update-азввдхостпул** .

Полный список поддерживаемых свойств и их значений по умолчанию см. в разделе [Поддерживаемые параметры RDP-файла](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать работу, выполните эти [ инструкции](powershell-module.md), чтобы настроить модуль PowerShell для Виртуального рабочего стола Windows и войти в Azure.

## <a name="default-rdp-properties"></a>Свойства RDP по умолчанию

По умолчанию опубликованные RDP-файлы содержат следующие свойства.

|Свойства RDP | Настольные системы | RemoteApps |
|---|---| --- |
| Режим с несколькими мониторами | Включено | Н/Д |
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
7. На вкладке **Свойства** перейдите в раздел **параметры RDP** , чтобы начать редактирование свойств RDP. Свойства должны быть представлены в формате, разделенном точкой с запятой, например в примерах PowerShell.
8. Когда все будет готово, нажмите кнопку **сохранить** , чтобы сохранить изменения.

В следующих разделах вы узнаете, как изменить настраиваемые свойства RDP вручную в PowerShell.

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

- [Подключение с использованием клиента классических приложений Windows](connect-windows-7-and-10.md)
- [Подключение с помощью веб-клиента](connect-web.md)
- [Подключение к клиенту Android](connect-android.md)
- [Подключение с помощью клиента macOS](connect-macos.md)
- [Подключение с помощью клиента iOS](connect-ios.md)
