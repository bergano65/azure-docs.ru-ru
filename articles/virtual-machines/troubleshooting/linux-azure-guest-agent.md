---
title: Устранение неполадок агента Linux для Azure
description: Устранение проблем с агентом Linux для Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 247324c30bbe0edaef78c0b0d5e6a6d593e8cac9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586403"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>Устранение неполадок агента Linux для Azure

[Агент Linux для Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) позволяет виртуальной машине взаимодействовать с контроллером структуры (базовым физическим сервером, на котором РАЗМЕЩЕНа виртуальная машина) на IP-адресе 168.63.129.16.

>[!NOTE]
>Этот IP-адрес является виртуальным общедоступным IP-адресом, который упрощает обмен данными и не должен блокироваться. Дополнительные сведения см. в разделе [что такое IP-адрес 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="before-you-begin"></a>Подготовка к работе

Проверьте состояние и версию агента, чтобы убедиться, что она все еще поддерживается. Инструкции по поиску состояния и [версии см.](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) в статье о [минимальной поддержке версий для агентов виртуальных машин в Azure](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version) .

## <a name="troubleshoot-a-not-ready-status"></a>Устранение неполадок состояния "не готово"

1. Проверьте состояние службы агента Linux для Azure, чтобы убедиться, что он работает. Имя службы может быть **walinuxagent** или **waagent**.

   ```
   root@nam-u18:/home/nam# service walinuxagent status
   ● walinuxagent.service - Azure Linux Agent
      Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
      Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
    Main PID: 1036 (python3)
       Tasks: 4 (limit: 4915)
      CGroup: /system.slice/walinuxagent.service
              ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
              └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers
   Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
   root@nam-u18:/home/nam#
   ```

   Если служба запущена, перезапустите ее, чтобы устранить проблему. Если служба остановлена, запустите ее, подождите несколько минут, а затем снова проверьте состояние.

1. Убедитесь, что включено автоматическое обновление. Проверьте параметр автоматического обновления в **/etc/waagent.conf**.

   ```
   AutoUpdate.Enabled=y
   ```

   Дополнительные сведения об обновлении агента Linux для Azure см. в [статье обновление агента Linux для Azure на виртуальной машине](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent).

1. Убедитесь, что виртуальная машина может подключаться к контроллеру структуры. Используйте такое средство, как фигурная скобка, чтобы проверить, может ли виртуальная машина подключаться к 168.63.129.16 через порты 80, 443 и 32526. Если виртуальная машина не подключается должным образом, проверьте, открыт ли Исходящая связь через порты 80, 443 и 32526 в локальном брандмауэре на виртуальной машине. Если этот IP-адрес заблокирован, агент виртуальной машины может отобразить непредвиденное поведение.

## <a name="advanced-troubleshooting"></a>Расширенный поиск проблем

События для устранения неполадок агента Linux для Azure записываются в файл **/Вар/лог/ваажент.лог** .

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Не удается подключиться к Виресервер IP (IP-адрес узла)

Следующая ошибка появляется в файле **/Вар/лог/ваажент.лог** , если виртуальная машина не может связаться с IP-адресом виресервер на сервере узла.

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

Для разрешения этой проблемы:

* Подключитесь к виртуальной машине с помощью SSH и попытайтесь получить доступ к следующему URL-адресу: http://168.63.129.16/?comp=versions .
* Проверьте наличие проблем, которые могут быть вызваны брандмауэром, прокси-сервером или другим источником, который может блокировать доступ к IP-адресу 168.63.129.16.
* Проверьте, блокирует ли IPTables Linux или брандмауэр стороннего производителя доступ к портам 80, 443 и 32526.

## <a name="next-steps"></a>Дальнейшие действия

Для дальнейшей диагностики проблем с агентом Linux в Azure [обратитесь в службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
