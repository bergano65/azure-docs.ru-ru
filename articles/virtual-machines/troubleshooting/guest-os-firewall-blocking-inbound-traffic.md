---
title: Входящий трафик блокируется брандмауэром гостевой ОС виртуальной машины Azure | Документация Майкрософт
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 6e90b164fac4ea1123f5f9a43eea1169d93d9a04
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154023"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Входящий трафик блокируется брандмауэром гостевой ОС виртуальной машины Azure

В этой статье объясняется, как устранить проблему на портале удаленного рабочего стола (RDP), которая возникает при блокировании входящего трафика брандмауэром гостевой операционной системы.

## <a name="symptoms"></a>Проблемы

Невозможно использовать протокол удаленного рабочего стола для подключения к виртуальной машине Azure. В разделе "Диагностика загрузки -> Снимок экрана" вы видите, что операционная система полностью загружена, на экране приветствия (Ctrl+Alt+Del).

## <a name="cause"></a>Причина:

### <a name="cause-1"></a>Причина 1

Не настроено правило RDP, которое разрешает трафик RDP.

### <a name="cause-2"></a>Причина 2

Настроенные профили брандмауэра гостевой ОС блокируют все входящие подключения, в том числе трафик RDP.

![Параметры брандмауэра](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Решение

Прежде чем выполнять какие-либо действия, сделайте моментальный снимок диска системы затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье  [Create a snapshot](../windows/snapshot-copy-managed-disk.md) (Создание моментального снимка).

Чтобы устранить такую проблему, используйте один из методов, описанных в статье [Use remote tools to troubleshoot Azure VM issues](remote-tools-troubleshoot-azure-vm-issues.md) (Применение инструментов удаленной отладки для устранения неполадок виртуальной машины Azure), чтобы создать удаленное подключение к виртуальной машине, а затем измените правила брандмауэра гостевой операционной системы, чтобы **разрешить** трафик RDP.

### <a name="online-troubleshooting"></a>Сетевое устранение неполадок

Подключитесь к [последовательной консоли и откройте экземпляр PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Если на виртуальной машине последовательная консоль не включена, перейдите к разделу [Автономное восстановление виртуальной машины](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Решение 1

1.  Если агент Azure установлен и нормально работает на виртуальной машине, вы можете применить действие "Сбросить только конфигурацию" из раздела **Поддержка и устранение неполадок** > **Сброс пароля** в меню виртуальной машины.

2.  Эта функция восстановления выполняет следующие действия.

    *   Включает компонент RDP, если он отключен.

    *   Включает профили брандмауэра Windows.

    *   Проверьте, что в брандмауэре Windows включено правило RDP.

    *   Если предыдущие шаги не сработали, вручную сбросьте правило брандмауэра. Для этого с помощью следующей команды получите список правил, которые содержат имя "Remote Desktop" (Удаленный рабочий стол):

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Если для протокола удаленного рабочего стола был задан любой порт, отличный от 3389, проверьте наличие настроенных пользовательских правил для этого порта. Чтобы получить все правила входящего трафика для определенного номера порта, выполните следующую команду:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Если вы увидите здесь отключенное правило, включите его. Чтобы открыть группу целиком, например встроенную группу удаленного рабочего стола, выполните следующую команду:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    В противном случае выполните следующую команду, чтобы открыть конкретное правило удаленного рабочего стола (TCP-In):

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Для устранения неполадок можно временно отключить профили брандмауэра:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Завершив устранение неполадок и правильно настроив брандмауэр, обязательно включите его снова.

    > [!Note]
    > Чтобы применить такие изменения, не нужно перезапускать виртуальную машину.

5.  Попробуйте создать RDP-подключение к виртуальной машине.

#### <a name="mitigation-2"></a>Решение 2

1.  Запросите профили брандмауэра и убедитесь, установлена ли для входящих подключений политика брандмауэра  *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Следующие рекомендации применяются к политике брандмауэра в зависимости от того, как она настроена:
    >    * *BlockInbound*: блокируется весь входящий трафик, если нет правил для разрешения этого трафика.
    >    * *BlockInboundAlways*: блокируется весь входящий трафик, а все правила брандмауэра игнорируются.

2.  Измените политику  *DefaultInboundAction* , чтобы эти профили  **разрешали**  весь трафик. Для этого выполните следующую команду:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Снова запросите данные о профилях и убедитесь, что изменения успешно применены. Для этого выполните следующую команду:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Чтобы применить такие изменения, не нужно перезапускать виртуальную машину.

4.  Снова попробуйте создать подключение к виртуальной машине по протоколу удаленного рабочего стола.

### <a name="offline-mitigations"></a>Автономные способы решения

1.  [Подключите системный диск к виртуальной машине восстановления](troubleshoot-recovery-disks-portal-windows.md).

2.  Установите подключение с помощью удаленного рабочего стола к виртуальной машине, используемой для восстановления.

3.  Убедитесь, что в консоли управления дисками подключенный диск имеет состояние  **подключенного** . Запишите или запомните букву диска, которая назначена подключенному системному диску.

#### <a name="mitigation-1"></a>Решение 1

См. статью  [Включение или отключение правила брандмауэра в гостевой ОС на виртуальной машине Azure](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Решение 2

1.  [Подключите системный диск к виртуальной машине восстановления](troubleshoot-recovery-disks-portal-windows.md).

2.  Установите подключение с помощью удаленного рабочего стола к виртуальной машине, используемой для восстановления.

3.  После подключения системного диска к виртуальной машине восстановления убедитесь, что в консоли управления дисками он помечен как  **подключенный к сети** . Запишите или запомните букву диска, которая присвоена подключенному диску ОС.

4.  Откройте экземпляр командной строки с повышенными привилегиями и выполните следующий скрипт:

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [Отсоедините системный диск и повторно создайте виртуальную машину](troubleshoot-recovery-disks-portal-windows.md).

6.  Убедитесь, что проблема устранена.
