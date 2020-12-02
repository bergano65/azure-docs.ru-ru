---
title: Устранение неполадок гостевого агента Linux в Azure
description: Устранение неполадок гостевого агента Linux в Azure — нерабочие проблемы
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
ms.openlocfilehash: fc609b60c9d5d4d4734c3d73cbda87935b533caf
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500263"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>Устранение неполадок гостевого агента Linux в Azure

[Гостевой агент Azure Linux](../extensions/agent-linux.md) — это агент виртуальной машины (ВМ). Она позволяет виртуальной машине взаимодействовать с контроллером структуры (базовым физическим сервером, на котором размещена виртуальная машина) на IP-адресе 168.63.129.16. Этот IP-адрес является виртуальным общедоступным IP-адресом, который упрощает обмен данными. Дополнительные сведения см. в разделе [что такое IP-адрес 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="checking-agent-status-and-version"></a>Проверка состояния и версии агента

См. раздел https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Устранение неполадок агента виртуальной машины, состояние которого не готов

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>Шаг 1. Проверьте, запущена ли служба гостевого агента Azure Linux

В зависимости от дистрибутив имя службы может быть walinuxagent или waagent:

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


Если вы видите службы и они работают, перезапустите службу, чтобы узнать, решена ли проблема. Если службы остановлены, запустите их и подождите несколько минут. Затем проверьте, сообщается ли **состояние агента** как **готовое**. Чтобы устранить эти проблемы, обратитесь в [Служба поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-enabled"></a>Шаг 2. Проверьте, включено ли автоматическое обновление

Проверьте этот параметр в/etc/waagent.conf:

```
AutoUpdate.Enabled=y
```

Дополнительные сведения об обновлении агента Linux для Azure см. в статье https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Шаг 3. Проверьте, может ли виртуальная машина подключаться к контроллеру структуры

Используйте такое средство, как фигурная скобка, чтобы проверить, может ли виртуальная машина подключаться к 168.63.129.16 через порты 80, 32526 и 443. Если виртуальная машина не подключается должным образом, проверьте, открыт ли Исходящая связь через порты 80, 443 и 32526 в локальном брандмауэре на виртуальной машине. Если этот IP-адрес будет заблокирован, агент виртуальных машин может показывать непредвиденную реакцию в различных сценариях.

## <a name="advanced-troubleshooting"></a>Расширенный поиск проблем

События для устранения неполадок гостевого агента Linux в Azure записываются в следующих файлах журнала:

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Не удается подключиться к Виресервер IP (IP-адрес узла) 

Обратите внимание на следующие записи об ошибках в/Вар/лог/ваажент.лог:

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**Анализ**

Виртуальная машина не может связаться с IP-адресом Виресервер на сервере узла.

**Решение**

1. Так как IP-адрес Виресервер недоступен, подключитесь к виртуальной машине по протоколу SSH, а затем попытайтесь получить доступ к следующему URL-адресу: http://168.63.129.16/?comp=versions 
1. Проверьте наличие проблем, которые могут быть вызваны брандмауэром, прокси-сервером или другим источником, который может блокировать доступ к IP-адресу 168.63.129.16.
1. Проверьте, блокирует ли IPTables Linux или брандмауэр стороннего производителя доступ к портам 80, 443 и 32526. Дополнительные сведения о том, почему этот адрес не должен блокироваться, см. в разделе [что такое IP Address 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="next-steps"></a>Дальнейшие действия

Для дальнейшей диагностики проблемы "Гостевой агент Windows Azure не работает" [обратитесь в службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).