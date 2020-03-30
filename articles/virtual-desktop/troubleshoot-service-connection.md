---
title: Устранение проблем сервисного подключения Windows Virtual Desktop - Azure
description: Как решить проблемы при настройке клиентских подключений в среде для арендаторов Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127458"
---
# <a name="windows-virtual-desktop-service-connections"></a>Соединения службы Windows Virtual Desktop

Используйте эту статью для решения проблем с подключением клиентов Windows Virtual Desktop.

## <a name="provide-feedback"></a>Отзывы

Вы можете дать нам обратную связь и обсудить Windows Virtual Desktop Service с командой продуктов и другими активными членами сообщества в [Windows Virtual Desktop Tech Community.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Пользователь подключается, но ничего не отображается (без канала)

Пользователь может запускать удаленных настольных клиентов и может проверить подлинность, однако пользователь не видит никаких иконок в ленте обнаружения веб.

Подтвердите, что пользователь, сообщающий о проблемах, был назначен группам приложений с помощью этой командной строки:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Подтвердите, что пользователь входит в систему с правильными учетными данными.

Если веб-клиент используется, подтвердите, что нет проблем с кэшированными учетными данными.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Многосессионные виртуальные машины Windows 10 Enterprise не отвечают

Если виртуальная машина не реагирует и вы не можете получить к ней доступ через RDP, вам нужно устранить ее с помощью функции диагностики, проверив состояние хоста.

Чтобы проверить состояние хоста, запустите этот cmdlet:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Если статус хоста, `NoHeartBeat`это означает, что VM не отвечает, и агент не может общаться с службой Windows Virtual Desktop.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Есть несколько вещей, которые вы можете сделать, чтобы исправить статус NoHeartBeat.

### <a name="update-fslogix"></a>Обновление FSLogix

Если FSLogix не в курсе, особенно если это версия 2.9.7205.27375 frxdrvvt.sys, это может привести к тупику. Убедитесь в том, чтобы [обновить FSLogix до последней версии](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Отключить BgTaskРегистрацияОбслуживание

Если обновление FSLogix не работает, проблема может заключаться в том, что компонент BiSrv истощает ресурсы системы во время еженедельной задачи обслуживания. Временно отключить задачу обслуживания, отключив BgTaskMaintenanceMaintenanceTask одним из этих двух методов:

- Перейдите в меню «Пуск» и ищите **список задач.** Перейдите к **библиотеке** > task Scheduler**Microsoft** > **Windows** > **BrokerInfrastructure.** Ищите задачу под названием **BgTaskРегистрацияОбслуживание.** Когда вы найдете его, правой кнопкой мыши и выбрать **отключить** из выпадающих меню.
- Откройте меню командной строки в качестве администратора и запустите следующую команду:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Дальнейшие действия

- Для обзора устранения неполадок Windows Virtual Desktop и треков эскалации [см.](troubleshoot-set-up-overview.md)
- Для устранения неполадок при создании пула арендатора и хоста в среде Windows Virtual Desktop [см.](troubleshoot-set-up-issues.md)
- Для устранения неполадок при настройке виртуальной машины (VM) в Windows Virtual Desktop [см.](troubleshoot-vm-configuration.md)
- Для устранения неполадок при использовании [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)PowerShell с Windows Virtual Desktop см.
- Чтобы пройти через учебник по устранению неполадок, [см.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
