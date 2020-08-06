---
title: Упреждающая настройка GRUB для Серийной консоли Azure | Документация Майкрософт
description: Настройте GRUB в различных дистрибутивах, разрешив доступ к однопользовательскому режиму и режиму восстановления на виртуальных машинах Azure.
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
ms.openlocfilehash: c48ef0321ece2e7e0ffcdfcb8c0907c5f839e738
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831368"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Предварительная настройка доступа к GRUB и sysrq может сэкономить много времени.

Наличие доступа к Серийной консоли и GRUB в большинстве случаев сокращает время восстановления виртуальной машины Linux (IaaS). GRUB предусматривает варианты восстановления виртуальной машины, которые в противном случае потребовали бы больше времени. 


Существует множество причин для восстановления виртуальной машины, которые можно отнести к следующим сценариям:

   - Повреждения файловой системы/ядра/основной загрузочной записи (MBR)
   - Сбои при обновлении ядра
   - Неправильные параметры ядра для GRUB
   - Неправильные конфигурации fstab
   - Конфигурации брандмауэра
   - Потеря пароля
   - Искаженные файлы конфигурации sshd
   - Конфигурации сети

 Многие другие сценарии можно найти [здесь](./serial-console-linux.md#common-scenarios-for-accessing-the-serial-console).

Убедитесь, что вы можете получить доступ к GRUB и Серийной консоли на виртуальных машинах, развернутых в Azure. 

Если вы не знакомы с Серийной консолью, см. [эту статью](./serial-console-linux.md).

> [!TIP]
> Обязательно создавайте резервные копии файлов перед внесением изменений.

Просмотрите видео ниже, чтобы увидеть, как можно быстро восстановить виртуальную машину Linux при наличии доступа к GRUB.

[Видео о восстановлении виртуальной машины Linux](https://youtu.be/KevOc3d_SG4)

Существует ряд методов восстановления виртуальных машин Linux. В облачной среде это сложный процесс.
Все действия с инструментами и функциями выполняются непрерывно, чтобы обеспечить быстрое восстановление служб.

Серийная консоль Azure позволяет взаимодействовать с виртуальной машиной Linux, как если бы вы работали в консоли системы.

Можно управлять множеством файлов конфигурации, включая конфигурацию загрузки ядра. 

Более опытные системные администраторы Linux или Unix оценят **однопользовательский** и **аварийный режимы**, доступные через Серийную консоль Azure, которые позволяют исключить процедуры замены диска и удаления виртуальной машины во многих сценариях восстановления.

Метод восстановления зависит от возникшей проблемы, например потерянный или неправильно размещенный пароль можно сбросить, выбрав в параметрах портала Azure команду **Сброс пароля**. Функция **Сброс пароля** называется расширением и взаимодействует с гостевым агентом Linux.

Доступны и другие расширения, например "Настраиваемый скрипт", однако для этих параметров должен быть запущен и находиться в работоспособном состоянии агент Linux **waagent**, что не всегда соответствует действительности.

![Состояние агента](./media/virtual-machines-serial-console/agent-status.png)


Обеспечение доступа к Серийной консоли Azure и GRUB означает, что смена пароля или неправильная конфигурация могут быть исправлены в течение нескольких минут, а не часов. Можно даже осуществить принудительную загрузку виртуальной машины с другим ядром (если на диске имеется несколько ядер) в сценарии, когда повреждается основное ядро.

![Несколько ядер](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Рекомендуемый порядок методов восстановления

- Серийная консоль Azure

- Замена диска — может быть автоматизирована с помощью следующих средств:

   - [скрипты восстановления PowerShell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [скрипты восстановления bash](https://github.com/sribs/azure-support-scripts)

- Устаревший метод

## <a name="disk-swap-video"></a>Видео о замене диска

Если у вас нет доступа к GRUB, просмотрите [это](https://youtu.be/m5t0GZ5oGAc) видео о том, как можно легко автоматизировать процедуру замены диска для восстановления виртуальной машины.

## <a name="challenges"></a>Сложности

Не для всех виртуальных машин Linux в Azure по умолчанию настроен доступ к GRUB, а также возможность прерывания работы с помощью команд sysrq. Для некоторых старых дистрибутивов, таких как SLES 11, не настроен вывод запроса на вход в Серийной консоли Azure.

В этой статье мы рассмотрим различные дистрибутивы Linux и способы настройки доступа к GRUB.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Настройка виртуальной машины Linux для приема команд SysRq
В некоторых более новых дистрибутивах Linux клавиша SysRq включена по умолчанию, в то время как в других она может быть настроена для приема значений только для определенных функций SysRq.
В более старых дистрибутивах она может быть полностью отключена.

Функция SysRq полезна для перезагрузки нерабочей или неотвечающей виртуальной машины непосредственно из Серийной консоли Azure, а также может пригодиться для получения доступа к меню GRUB. В противном случае перезапуск виртуальной машины из другого окна портала или сеанса SSH может привести к разрыву текущего подключения консоли и истечению времени ожидания GRUB, которое используется для вывода меню GRUB.
Виртуальную машину необходимо настроить таким образом, чтобы она принимала для параметра ядра значение 1, которое включает все функции sysrq, или 128, которое разрешает перезагрузку и выключение.


[Видео о включении sysrq](https://youtu.be/0doqFRrHz_Mc)


Чтобы настроить для виртуальной машины возможность перезагрузки с помощью команд SysRq на портал Azure, необходимо задать значение 1 для параметра ядра kernel.sysrq.

Чтобы эта конфигурация сохранилась после перезагрузки, добавьте запись в файл **sysctl.conf**.

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Также возможна динамическая настройка параметра ядра.

`sysctl -w kernel.sysrq=1`

Если у вас нет доступа с правами **root** или не работает sudo, настроить sysrq из командной строки оболочки будет невозможно.

В этом сценарии можно включить sysrq, используя портал Azure. Этот метод может быть полезен, если файл **sudoers.d/waagent** поврежден или удален.

Для использования функции RunShellScript на портале Azure (Операции -> Выполнить команду) необходимо, чтобы процесс waagent был работоспособен. Затем можно вставить эту команду, чтобы включить sysrq.

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Как показано ниже: ![enable sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

После завершения можно попытаться получить доступ к **sysrq** и проверить возможность перезагрузки.

![enable sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Выберите пункт **Перезагрузка** и нажмите кнопку **Отправить SysRq**.

![enable sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

Система должна записать в журнал сообщение о сбросе, как показано ниже:

![enable sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Настройка GRUB для Ubuntu

По умолчанию доступ к GRUB можно получить, удерживая нажатой клавишу **ESC** во время загрузки виртуальной машины. Если меню GRUB не появится, можно вызвать его принудительно в Серийной консоли Azure с помощью одного из следующих способов.

**Вариант 1**. Принудительное отображение GRUB на экране 

Обновите файл /etc/default/grub.d/50-cloudimg-settings.cfg, чтобы меню GRUB оставалось на экране в течение указанного времени ожидания.
Нажимать клавишу **ESC** не требуется, так как GRUB отобразится немедленно.

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Вариант 2**. Разрешение нажатия клавиши **ESC** перед загрузкой

Аналогичного эффекта можно добиться, внеся изменения в файл /etc/default/grub, после чего время ожидания нажатия клавиши **ESC** составит 3 секунды.


Закомментируйте эти две строки:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
и добавьте следующую строку:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 разрешает доступ к серийной консоли, но не предоставляет возможности взаимодействия. Запрос **login:** не отображается.


Чтобы получить запрос **login:** в версии 12.04, выполните следующее:
1. Создайте файл с именем /etc/init/ttyS0.conf, содержащий следующий текст:

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

2. Запустите getty с помощью upstart:     
    ```
    sudo start ttyS0
    ```
 
Параметры, необходимые для настройки серийной консоли для версий Ubuntu, можно найти [здесь](https://help.ubuntu.com/community/SerialConsoleHowto).

## <a name="ubuntu-recovery-mode"></a>Режим восстановления Ubuntu

Через GRUB для Ubuntu доступны дополнительные параметры восстановления и очистки, однако это возможно лишь в том случае, если соответствующим образом настроены параметры ядра.
Если при настройке этого параметра загрузки ядра произойдет сбой, меню восстановления будет принудительно отправлено в Диагностику Azure, а не в Серийную консоль Azure.
Чтобы получить доступ к меню восстановления Ubuntu, выполните указанные ниже действия.

Прервите процесс BOOT и откройте меню GRUB.

Выберите "Advanced Options for Ubuntu" и нажмите клавишу ВВОД.

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Выберите строку с надписью *(recovery mode)* . Не нажимайте клавишу ВВОД, а нажмите клавишу Е.

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Найдите строку, которая загружает ядро, и замените последний параметр **nomodeset** на **console=ttyS0**.

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Нажмите **CTRL+Х**, чтобы запустить и загрузить ядро.
Если все получится, вы увидите дополнительные параметры для других вариантов восстановления.

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Настройка GRUB для Red Hat

## <a name="red-hat-74-grub-configuration"></a>Настройка GRUB для Red Hat 7\.4\+ или более поздней версии
Используемый по умолчанию файл конфигурации /etc/default/grub для этих версий настроен правильно.

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

Включите клавишу SysRq:

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Настройка GRUB для Hat 7\.2 и 7\.3
Необходимо внести изменения в файл /etc/default/grub. Конфигурация по умолчанию выглядит, как показано в следующем примере:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Измените следующие строки в файле /etc/default/grub:

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

и добавьте следующую строку:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

Теперь файл /etc/default/grub должен выглядеть примерно так:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Завершите и обновите конфигурацию GRUB с помощью следующей команды:

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Задайте параметр ядра SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Можно также настроить GRUB и SysRq с помощью одной строки либо в оболочке, либо с помощью операции "Выполнить команду". Создайте резервные копии файлов перед выполнением следующей команды:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Настройка GRUB для Red Hat 6\.x
Необходимо внести изменения в файл /boot/grub/grub.conf. Значение `timeout` определяет, как долго отображается меню GRUB.

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


Последняя строка *terminal –-timeout=5 serial console* увеличивает время ожидания **GRUB**, добавляя запрос **Press any key to continue**, отображаемый в течение 5 секунд.

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

Меню GRUB должно отображаться на экране в течение заданного времени ожидания (timeout=15) без необходимости нажатия клавиши ESC. Не забудьте щелкнуть "Консоль в браузере", чтобы сделать активным меню и выбрать необходимое ядро.

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 SP1
Либо используйте загрузчик YaST в соответствии с официальной [документацией](./serial-console-grub-single-user-mode.md#grub-access-in-suse-sles).

Либо добавьте или измените в файле /etc/default/grub следующие параметры:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Убедитесь, что в параметре GRUB_CMDLINE_LINUX или GRUB_CMDLINE_LINUX_DEFAULT используется значение ttys0:

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Повторно создайте файл grub.cfg:

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
Серийная консоль появляется и отображает сообщения о загрузке, но не выводит запрос **login:** .

Откройте сеанс SSH на виртуальной машине и обновите файл **/etc/inittab**, раскомментировав следующую строку:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Затем выполните следующую команду: 

`telinit q`

Чтобы включить GRUB, внесите следующие изменения в файл /boot/grub/menu.lst: 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 При такой конфигурации сообщение **Press any key to continue** будет отображаться в консоли в течение 5 секунд. 

Затем в течение дополнительных 5 секунд будет отображаться меню GRUB. Нажав клавишу СТРЕЛКА ВНИЗ, вы прервете счетчик и сможете выбрать ядро, которое нужно загрузить. Можно также добавить ключевое слово **single**, чтобы включить однопользовательский режим, для которого требуется установить пароль для доступа с правами root.

Если добавить команду **init=/bin/bash**, ядро загрузится, однако программа init будет заменена оболочкой bash.

Вы получите доступ к оболочке без ввода пароля. Затем можно перейти к обновлению пароля для учетных записей Linux или внесению других изменений в конфигурацию.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Перевод ядра в режим командной строки bash
Наличие доступа к GRUB позволяет прерывать процесс инициализации. Такая возможность может быть полезна для многих процедур восстановления.
Если у вас нет пароля для доступа с правами root, который требуется для однопользовательского режима, можно загрузить ядро, заменив программу init командной строкой bash. Для этого необходимо добавить команду init=/bin/bash в строку загрузки ядра.

![bash1](./media/virtual-machines-serial-console/bash1.png)

Переподключите файловую систему / (root) с правами на чтение и запись (RW) с помощью следующей команды:

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


Теперь можно изменить пароль для доступа с правами root и внести множество других изменений в конфигурацию Linux.

![bash3](./media/virtual-machines-serial-console/bash3.png)

Перезапустите виртуальную машину с помощью следующей команды: 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Однопользовательский режим

Вам также может потребоваться доступ к виртуальной машине в однопользовательском или аварийном режиме. Выберите ядро, которое вы хотите загрузить или прервать, с помощью клавиш со стрелками.
Введите нужный режим, добавив ключевое слово **single** или **1** в строку загрузки ядра. В системах RHEL можно также добавить **rd.break**.

Дополнительные сведения о доступе к однопользовательскому режиму см. в [этом документе](./serial-console-grub-single-user-mode.md#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о [Серийной консоли Azure]( ./serial-console-linux.md)