---
title: Активная конфигурация GRUB для последовательной консоли Azure | Документация Майкрософт
description: Настройте GRUB в различных дистрибутивах, разрешив доступ к отдельным пользователям и режиму восстановления на виртуальных машинах Azure.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: vilibert
ms.openlocfilehash: a154ab4742f0d0d7acae0376bcf894bc2b62b4cd
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186933"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Упреждающее обеспечение того, что у вас есть доступ к GRUB и сисрк, может сэкономить много времени.

Наличие доступа к последовательной консоли и GRUB повысит время восстановления виртуальной машины IaaS Linux в большинстве случаев. GRUB предлагает варианты восстановления, которые в противном случае потребуют больше времени для восстановления виртуальной машины. 


Причины для восстановления виртуальных машин являются множеством, и их можно присвоить атрибутам следующие сценарии:

   - Поврежденные файловые системы/ядро/MBR (основная загрузочная запись)
   - Сбой обновления ядра
   - Неверные параметры ядра GRUB
   - Неправильные конфигурации fstab
   - Конфигурации брандмауэра
   - Потерянный пароль
   - Файлы искаженных конфигураций sshd
   - Конфигурации сети

 Многие другие сценарии, описанные [здесь](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)

Убедитесь, что вы можете получить доступ к GRUB и Серийная консоль на виртуальных машинах, развернутых в Azure. 

Если вы не знакомы с последовательной консолью, обратитесь к [этой ссылке](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> Обязательно создавайте резервные копии файлов перед внесением изменений

Просмотрите это видео ниже, чтобы увидеть, как можно быстро восстановить виртуальную машину Linux после получения доступа к GRUB.

[Восстановление видео виртуальной машины Linux](https://youtu.be/KevOc3d_SG4)

Существует ряд методов, помогающих восстановить виртуальные машины Linux. В облачной среде этот процесс был сложным.
Ход выполнения постоянно выполняется для средств и функций, чтобы обеспечить быстрое восстановление служб.

С помощью последовательной консоли Azure можно взаимодействовать с виртуальной машиной Linux, как если бы вы работали в консоли системы.

Вы можете управлять множеством файлов конфигурации, включая способ загрузки ядра. 

Более опытные администраторы Linux/Unix sys оценят **однопользовательский** и **аварийный режимы** , доступные через последовательную консоль Azure для переключения дисков и удаления виртуальных машин для многих сценариев восстановления с избыточностью.

Метод восстановления зависит от того, какая проблема возникла, например, потерянный или неправильно установленный пароль можно сбросить с помощью параметров портал Azure параметры — > **Сброс пароля**. Функция **сброса пароля** называется расширением и взаимодействует с гостевым агентом Linux.

Другие расширения, такие как пользовательский скрипт, доступны, однако для этих параметров требуется, чтобы **Waagent** Linux были работоспособными и в рабочем состоянии, что не всегда так.

![состояние агента](./media/virtual-machines-serial-console/agent-status.png)


Обеспечение доступа к последовательной консоли Azure и GRUB означает, что в течение нескольких минут вместо часов может быть исправить изменение пароля или неправильная конфигурация. Можно даже заставить виртуальную машину загружаться с альтернативного ядра, если на диске имеется несколько ядер, в которых основной ядро будет повреждено.

![несколько ядер](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Предлагаемый порядок методов восстановления:

- Серийная консоль Azure

- Переключение дисков — может быть автоматизировано с помощью следующих средств:

   - [Сценарии восстановления PowerShell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [Скрипты Bash Recovery](https://github.com/sribs/azure-support-scripts)

- Устаревший метод

## <a name="disk-swap-video"></a>Видео о переключении диска:

Если у вас нет доступа к GRUB, просмотрите [это](https://youtu.be/m5t0GZ5oGAc) видео и посмотрите, как можно легко автоматизировать процедуру переключения диска для восстановления виртуальной машины.

## <a name="challenges"></a>Стоящие

Не все виртуальные машины Azure Linux настроены по умолчанию для доступа к GRUB, и ни один из них не настроен для прерывания с помощью команд сисрк. Некоторые старые дистрибутивов, такие как SLES 11, не настроены для вывода запроса на вход в последовательной консоли Azure.

В этой статье мы рассмотрим различные дистрибутивы Linux и конфигурации документов, в которых можно сделать GRUB доступным.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Как настроить виртуальную машину Linux для приема ключей Сисрк
Ключ сисрк включен в некоторых более новых дистрибутивовах Linux по умолчанию, хотя на других он может быть настроен для приема значений только для определенных функций Сисрк.
В более старых дистрибутивов может быть полностью отключена.

Функция Сисрк полезна для перезагрузки аварийно завершенной или зависой виртуальной машины непосредственно из последовательной консоли Azure, которая также полезна при получении доступа к меню GRUB. Кроме того, перезапуск виртуальной машины из другого окна портала или сеанса SSH может привести к удалению текущего подключения консоли. тем самым истечением времени ожидания GRUB, которые используются для вывода меню GRUB.
Виртуальная машина должна быть настроена на принятие значения 1 для параметра ядра, который включает все функции сисрк или 128, допускающие перезагрузку/выключена


[Включить видео сисрк](https://youtu.be/0doqFRrHz_Mc)


Чтобы настроить виртуальную машину на принятие перезагрузки через команды Сисрк на портал Azure, необходимо задать значение 1 для параметра ядра Kernel. сисрк

Чтобы эта конфигурация сохранила перезагрузку, добавьте запись в файл **сисктл. conf.**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Динамическая настройка параметра ядра

`sysctl -w kernel.sysrq=1`

Если нет доступа к **корневому каталогу** или sudo, то невозможно настроить сисрк в командной строке оболочки.

В этом сценарии можно включить сисрк, используя портал Azure. Этот метод может быть полезен, если файл **sudo. d/waagent** был поврежден или был удален.

При использовании функции портал Azure Operations-> Run > Руншеллскрипт необходимо, чтобы процесс waagent был работоспособен. Затем можно добавить эту команду, чтобы включить сисрк

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Как показано ниже: ![Enable sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

После завершения можно попробовать получить доступ к **сисрк** и увидеть, что возможна перезагрузка.

![включить sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Выберите команду " **Перезагрузка** " и " **Отправить сисрк** "

![включить sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

Система должна записать в журнал сообщение о сбросе, например

![включить sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Конфигурация Ubuntu GRUB

По умолчанию доступ к GRUB можно получить, удерживая клавишу **ESC** во время загрузки виртуальной машины. Если меню GRUB не отображается, вы можете принудительно сохранить меню GRUB на экране в последовательной консоли Azure с помощью одного из следующих параметров.

**Вариант 1** . принудительное отображение GRUB на экране 

Обновите файл/etc/default/grub.d/50-cloudimg-Settings.cfg, чтобы в течение указанного времени ожидания меню GRUB оставалось на экране.
Нажатие **клавиши ESC** не требуется, так как GRUB будет отображаться немедленно

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Вариант 2** . позволяет нажать **клавишу ESC** перед загрузкой

Аналогичное поведение может быть вызвано внесением изменений в файл/etc/default/grub и наблюдением за 3-секундным временем ожидания нажатием **клавиши ESC** .


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

Ubuntu 12,04 разрешает доступ к последовательной консоли, но не предоставляет возможности взаимодействия. **Имя входа:** запрос не отображается


Чтобы получить **имя входа** для 12,04, выполните следующую команду:
1. Создайте файл с именем/etc/init/ttyS0.conf, содержащий следующий текст:

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

2. Запрос на запуск жетти     
    ```
    sudo start ttyS0
    ```
 
Параметры, необходимые для настройки последовательной консоли для версий Ubuntu, можно найти [здесь](https://help.ubuntu.com/community/SerialConsoleHowto) .

## <a name="ubuntu-recovery-mode"></a>Режим восстановления Ubuntu

Дополнительные параметры восстановления и очистки доступны для Ubuntu через GRUB, однако эти параметры доступны только в том случае, если параметры ядра настроены соответствующим образом.
Сбой настройки параметра загрузки ядра приведет к принудительной отправке меню восстановления на система диагностики Azure, а не на последовательную консоль Azure.
Доступ к меню восстановления Ubuntu можно получить, выполнив следующие действия.

Прерывание процесса загрузки и доступа к меню GRUB

Выберите дополнительные параметры для Ubuntu и нажмите клавишу ВВОД.

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Выберите отображаемую строку *(режим восстановления)* не нажимайте клавишу ВВОД, но нажмите клавишу "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Укажите строку, которая будет загружать ядро, и подставьте **последний параметр без параметров в значение** **Console = ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Нажмите клавиши **Ctrl + x** , чтобы запустить и загрузить ядро.
Если все пойдет хорошо, вы увидите эти дополнительные параметры, которые помогут выполнить другие варианты восстановления.

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Конфигурация GRUB Red Hat

## <a name="red-hat-74-grub-configuration"></a>Red Hat 7\.4\+ конфигурация GRUB
Конфигурация/etc/default/grub по умолчанию для этих версий настроена надлежащим образом

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

Включение ключа Сисрк

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat 7\.2 и 7\.3 Конфигурация GRUB
Файл для изменения —/etc/default/grub — конфигурация по умолчанию выглядит как в следующем примере:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Измените следующие строки в/etc/default/grub

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

Также добавьте следующую строку:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

Теперь/etc/default/grub должен выглядеть примерно так:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Завершите и обновите конфигурацию GRUB с помощью

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Задайте параметр ядра Сисрк:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Можно также настроить GRUB и Сисрк с помощью одной строки либо в оболочке, либо с помощью команды Run. Создайте резервную копию файлов перед выполнением этой команды:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Конфигурация GRUB Red Hat 6\.x
Файл для изменения —/Бут/груб/груб.конф. Значение `timeout` определяет, как долго будет отображаться GRUB.

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


Последняя строка *терминала – timeout = 5 последовательной консоли* увеличит время ожидания **GRUB** , добавив запрос в 5 секунд на экран, **чтобы продолжить.**

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

Меню GRUB должно отображаться на экране для заданного времени ожидания = 15 без необходимости нажатия клавиши ESC. Не забудьте щелкнуть консоль в браузере, чтобы сделать активным меню и выбрать необходимый ядру.

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 SP1
Либо используйте загрузчик YaST в соответствии с официальными [документами](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles) .

Или добавьте или измените, чтобы/etc/default/grub следующие параметры:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Убедитесь, что ttyS0 используется в GRUB_CMDLINE_LINUX или GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Повторное создание GRUB. cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 или более поздней версии 
Появится последовательная консоль, в которой отображаются сообщения загрузки, но не отображается **имя входа: запрос.**

Откройте сеанс SSH в виртуальной машине и обновите файл **/etc/inittab** , отменив комментарий этой строки:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Далее выполните команду. 

`telinit q`

Чтобы включить GRUB, необходимо внести следующие изменения в/Boot/GRUB/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Эта конфигурация включит сообщение **нажмите любую клавишу, чтобы продолжить** отображение в консоли в течение 5 секунд. 

Затем в меню GRUB будет отображено дополнительное 5 секунд — при нажатии клавиши со стрелкой вниз счетчик будет прерываться и выбрать ядро, которое нужно загрузить. Добавьте ключевое слово **Single** для однопользовательского режима, для которого требуется установить корневой пароль.

Добавление команды **init = отличное/bin/bash** приведет к загрузке ядра, но гарантирует, что программа init будет заменена оболочкой bash.

Вы получите доступ к оболочке без ввода пароля. Затем можно перейти к обновлению пароля для учетных записей Linux или внести другие изменения в конфигурацию.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Принудительная работа ядра в командной строке Bash
Наличие доступа к GRUB позволяет прерывать процесс инициализации. это взаимодействие полезно для многих процедур восстановления.
Если у вас нет корневого пароля и для одного пользователя требуется корневой пароль, можно загрузить ядро, заменяющее программу init, с помощью командной строки Bash — это прерывание можно достигнуть, добавив init = отличное/bin/bash в загрузочную строку ядра.

![bash1](./media/virtual-machines-serial-console/bash1.png)

Подключите/(корневую) файловую систему с помощью команды

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


Теперь можно выполнять изменение корневого пароля или многие другие изменения конфигурации Linux.

![bash3](./media/virtual-machines-serial-console/bash3.png)

Перезапустите виртуальную машину с помощью 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Однопользовательский режим

Кроме того, может потребоваться доступ к виртуальной машине в режиме "один пользователь или аварийный режим". Выберите ядро, которое вы хотите загрузить или прервать с помощью клавиш со стрелками.
Введите нужный режим, добавив ключевое слово **Single** или **1** в загрузочную строку ядра. В RHEL Systems можно также добавить **Rd. Break**.

Дополнительные сведения о доступе к однопользовательскому режиму см. в [этом документе](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) . 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о [последовательной консоли Azure]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)