---
title: Проактивная конфигурация GRUB Серийной Консоли Azure Документы Майкрософт
description: Налаживание GRUB в различных дистрибутивах, позволяющих одному пользователю и режиму восстановления получить доступ в виртуальные машины Azure.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: 573bd0797e63fc512e59b0e0882c718e4569111c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262899"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Проактивное обеспечение доступа к GRUB и sysrq может сэкономить много времени простоя

Имея доступ к серийной консоли и GRUB улучшит время восстановления вашей IaaS Linux Виртуальная машина в большинстве случаев. GRUB предлагает варианты восстановления, которые в противном случае займет больше времени, чтобы восстановить ваш VM. 


Причины для выполнения восстановления VM много и могут быть отнесены к сценариям, таким как:

   - Коррумпированные файловые системы/ядро/MBR (Master Boot Record)
   - Неудачное обновление ядра
   - Неправильные параметры ядра GRUB
   - Неправильные конфигурации fstab
   - Конфигурации брандмауэра
   - Потерянный пароль
   - Изуродованные файлы конфигураций sshd
   - Конфигурации сетей

 Многие другие сценарии, [описанные здесь](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)

Убедитесь, что вы можете получить доступ к GRUB и консоли Serial на ваших всакантных в Azure. 

Если вы новичок в Serial Console, обратитесь к [этой ссылке.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/)

> [!TIP]
> Убедитесь, что вы берете резервные копирования файлов перед внесением изменений

Смотреть это видео ниже, чтобы увидеть, как вы можете быстро восстановить Linux VM, как только у вас есть доступ к GRUB

[Восстановление Linux VM Видео](https://youtu.be/KevOc3d_SG4)

Существует ряд методов, помогающих восстановить Linux VMs. В среде облака этот процесс является сложным.
Постоянно осуществляется прогресс в области инструментария и функций для обеспечения быстрого восстановления служб.

С Azure Serial Console вы можете взаимодействовать с Linux VM, как если бы вы были на консоли системы.

Вы можете манипулировать многими файлами конфигурации, включая то, как ядро будет загружаться. 

Более опытные аналитики Linux/Unix sys оценят **один пользователь** и **режимы экстренной помощи,** доступные через серийную консоль Azure, что делает Disk Swap и VM удаление для многих сценариев восстановления излишними.

Метод восстановления зависит от проблемы, например, потерянный или неуместный пароль может быть спрометен через параметры портала Azure -> **Сбросить пароль.** Функция **«Пароль сназа»** известна как расширение и общается с агентом Linux Guest.

Другие расширения, такие как пользовательский сценарий доступны однако эти параметры требуют, чтобы Linux **waagent** быть и в здоровом состоянии, которое не всегда так.

![статус агента](./media/virtual-machines-serial-console/agent-status.png)


Обеспечение доступа к последовательной консоли Azure и GRUB означает, что изменение пароля или неправильная конфигурация могут быть исправлены в считанные минуты, а не за несколько часов. Вы даже можете заставить VM загрузиться из альтернативного ядра, если у вас есть несколько ядер на диске в сценарии, где ваше основное ядро становится поврежденным.

![мульти ядро](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Предлагаемый порядок методов восстановления:

- Серийная консоль Azure

- Disk Swap - может быть автоматизирован с помощью либо:

   - [Скрипты восстановления энергетической оболочки](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [Баш Восстановление Скрипты](https://github.com/sribs/azure-support-scripts)

- Устаревший метод

## <a name="disk-swap-video"></a>Диск Своп Видео:

Если у вас нет доступа к GRUB смотреть [это](https://youtu.be/m5t0GZ5oGAc) видео и посмотреть, как вы можете легко автоматизировать процедуру своп диска, чтобы восстановить ваш VM

## <a name="challenges"></a>Проблемы:

Не все VMs Linux Azure настроены по умолчанию для доступа gRUB, и они не настроены для прерывания с помощью команд sysrq. Некоторые старые дистро, такие как SLES 11, не настроены для отображения запроса входа в серийную консоль Azure

В этой статье мы рассмотрим различные дистрибутивы Linux и конфигурации документов о том, как сделать GRUB доступным.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Как настроить Linux VM, чтобы принять клавиши SysRq
Ключ sysrq включен на некоторых новых дистро Linux по умолчанию, хотя на других он может быть настроен для принятия значений только для некоторых функций SysRq.
На старых дистро, он может быть отключен полностью.

Функция SysRq полезна для перезагрузки разбившегося или повешенного VM непосредственно с серийной консоли Azure, также полезной для получения доступа к меню GRUB, альтернативное перезапуск VM из другого окна портала или сеанс ssh может упасть с текущего подключения консоли, таким образом, истекающий GRUB Тайм-ауты, к которым используются для отображения меню GRUB.
VM должен быть настроен, чтобы принять значение 1 для параметра ядра, что позволяет все функции sysrq или 128, что позволяет перезагрузить / poweroff


[Включить sysrq видео](https://youtu.be/0doqFRrHz_Mc)


Чтобы настроить VM для приема перезагрузки с помощью команд SysRq на портале Azure, необходимо установить значение 1 для параметра ядра.sysrq

Для того чтобы эта конфигурация сохранилась перезагрузка, добавьте запись в файл **sysctl.conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Динамически настроить параметр ядра

`sysctl -w kernel.sysrq=1`

Если у вас нет **корневого** доступа или судо сломан, не будет возможно настроить sysrq из запроса оболочки.

В этом сценарии можно включить sysrq с помощью портала Azure. Этот метод может быть полезным, если **sudoers.d/waagent** файл стал сломанным или был удален.

Использование azure портала Операции -> Выполнить команды -> RunShellScript функции, требует, чтобы процесс waagent быть здоровым вы можете ввести эту команду, чтобы включить sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Как показано ![здесь: включить sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

После завершения, вы можете попробовать доступ к **sysrq** и должны увидеть, что перезагрузка возможна.

![включить sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Выберите **перезагрузку** и **отправить SysRq** команды

![включить sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

Система должна войти в сообщение сготовона, например,

![включить sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Конфигурация Ubuntu GRUB

По умолчанию вы должны иметь доступ к GRUB, удерживая ключ **Esc** во время загрузки VM, если меню GRUB не представлено, вы можете заставить и сохранить меню GRUB на экране в Azure Serial Console, используя один из этих вариантов.

**Вариант 1** - Силы GRUB будет отображаться на экране 

Обновление файла /etc/default/grub.d/50-cloudimg-settings.cfg, чтобы сохранить меню GRUB на экране для указанного TIMEOUT.
Вы не обязаны ударить **Esc,** как GRUB будет отображаться немедленно

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Вариант 2** - Позволяет нажать **на Esc** перед загрузкой

Аналогичное поведение можно испытать, внося изменения в файл /etc/default/grub и наблюдать 3-секундный тайм-аут, чтобы ударить **Esc**


Прокомментируйте эти две строки:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
и добавить эту строку:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Увунту\.12 04

Ubuntu 12.04 позволит получить доступ к серийной консоли, но не предлагает возможность взаимодействовать. **Войти:** запрос не виден


Для получения **входа в систему** за 12.04 подсказка:
1. Создайте файл под названием /etc/init/ttyS0.conf, содержащий следующий текст:

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. Попросите выскочка, чтобы начать гетти     
    ```
    sudo start ttyS0
    ```
 
Настройки, необходимые для настройки серийной консоли для версий Ubuntu, можно найти [здесь](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Режим восстановления Ubuntu

Дополнительные варианты восстановления и очистки доступны для Ubuntu через GRUB, однако эти настройки доступны только при настройке параметров ядра соответствующим образом.
Неспособность настроить этот параметр загрузки ядра заставит меню восстановления быть отправлено в Azure Diagnostics, а не в серийную консоль Azure.
Вы можете получить доступ к меню восстановления Ubuntu, высоблюдая следующие действия:

Прервать процесс BOOT и получить доступ к меню GRUB

Выберите расширенные опции для Ubuntu и нажмите введите

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Выберите строку отображения *(режим восстановления)* не нажмите введите, но нажмите "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Найдите линию, которая загрузит ядро и заменит последний параметр **номодетс** а назначения в качестве **консоли**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Нажмите **Ctrl-x,** чтобы запустить и загрузить ядро.
Если все пойдет хорошо, вы увидите эти дополнительные параметры, которые могут помочь выполнить другие варианты восстановления

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Конфигурация Red Hat GRUB

## <a name="red-hat-74-grub-configuration"></a>Конфигурация\.Red\+ Hat 7 4 GRUB
Конфигурация по умолчанию /etc/default/grub на этих версиях адекватно настроена

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Включить ключ SysRq

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Конфигурация\.Red Hat\.7 2 и 7 3 GRUB
Файл для изменения /etc/default/grub - конфигурация по умолчанию выглядит следующим образом:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Измените следующие строки в /etc/default/grub

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

Также добавьте эту строку:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub теперь должны выглядеть так же, как на этом примере:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Полное и обновление конфигурации GRUB с использованием

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Установите параметр ядра SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Вы можете настроить GRUB и SysRq с помощью одной строки либо в оболочке, либо через команду Run. Резервное копирование файлов перед запуском этой команды:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Конфигурация\.Red Hat 6 x GRUB
Файл для изменения /boot/grub/grub.conf. Значение `timeout` будет определять, как долго GRUB отображается для.

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


Последняя линия *терминала - тайм-аут-5 серийная консоль* будет способствовать дальнейшему увеличению **GRUB** тайм-аут, добавив запрос 5 секунд отображения **Нажмите любой ключ для продолжения.**

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

GRUB меню должно отображаться на экране для настроенного тайм-аута No15 без необходимости нажатия Esc. Убедитесь в том, чтобы нажать в консоли в браузере, чтобы сделать активное меню и выбрать необходимое ядро

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>Suse

## <a name="sles-12-sp1"></a>SLES 12 sp1
Либо использовать yast загрузки в рамках официальных [документов](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles)

Или добавить / изменить в / и т.д./по умолчанию / GRUB следующие параметры:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Проверить, что ttys0 используется в GRUB_CMDLINE_LINUX или GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Воссоздать grub.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 или более поздней версии 
Серийная консоль отображает и отображает загрузочные сообщения, но не отображает **логин:** запрос

Откройте сеанс ssh в VM и обновите файл **/etc/inittab,** не комментируя эту строку:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Следующий запуск команды 

`telinit q`

Для включения GRUB следует внести следующие изменения в /boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Эта конфигурация позволит сообщению **Press любой ключ продолжать** появляться на консоли в течение 5 секунд 

Затем он будет отображать меню GRUB в течение дополнительных 5 секунд - при нажатии вниз стрелка вы будете прерывать счетчик и выбрать ядро вы хотите загрузить либо приложение **одно ключевое** слово для одного пользовательского режима, который требует корневой пароль, который будет установлен.

Приложение команды **init'/bin/bash** загрузит ядро, но гарантирует, что программа init будет заменена оболочкой bash.

Вы получите доступ к оболочке без необходимости вводить пароль. Затем можно приступить к обновлению пароля для учетных записей Linux или внести другие изменения конфигурации.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Заставить ядро к Баш запрос
Наличие доступа к GRUB позволяет прервать процесс инициализации, это взаимодействие полезно для многих процедур восстановления.
Если у вас нет корневого пароля и один пользователь требует, чтобы у вас был корневой пароль, вы можете загрузить ядро, заменяя программу init с помощью запроса Bash - этот прерывание может быть достигнуто путем податливого init'/bin/bash к линии загрузки ядра

![bash1](./media/virtual-machines-serial-console/bash1.png)

Remount вашей / (корень) файловой системы RW с помощью команды

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


Теперь вы можете выполнить изменение корневого пароля или многие другие изменения конфигурации Linux

![bash3](./media/virtual-machines-serial-console/bash3.png)

Перезапустить VM с 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Режим единого пользователя

Кроме того, возможно, потребуется доступ к VM в режиме одного пользователя или аварийной ситуации. Выберите ядро, необходимое для загрузки или прерывания использования клавиш со стрелками.
Введите желаемый режим, приложив **ключевое** слово один или **1** к линии загрузки ядра. На системах RHEL, вы также можете прибвыться **rd.break**.

Для получения дополнительной информации о том, как получить доступ к одному пользовательскому режиму, [см.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о [серийной консоли Azure]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
