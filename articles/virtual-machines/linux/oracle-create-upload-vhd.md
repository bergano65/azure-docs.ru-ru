---
title: Создание и передача виртуального жесткого диска Oracle Linux
description: Узнайте, как создать и передать виртуальный жесткий диск (VHD-файл) Azure, содержащий операционную систему Oracle Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/10/2019
ms.author: guybo
ms.openlocfilehash: 4e9f87ec4a0df28b14bca8f5a44dfe9388e8ff03
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500535"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Подготовка виртуальной машины Oracle Linux для Azure

В этой статье предполагается, что вы уже установили операционную систему Oracle Linux на виртуальный жесткий диск. Существует несколько средств для создания VHD-файлов, например решение для виртуализации, такое как Hyper-V. Инструкции см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="oracle-linux-installation-notes"></a>Замечания по установке Oracle Linux
* Дополнительные сведения о подготовке Linux для Azure см. в разделе [Общие замечания по установке Linux](create-upload-generic.md#general-linux-installation-notes).
* Hyper-V и Azure поддерживают Oracle Linux с ядром Unbreakable Enterprise Kernel (UEK) или ядром, совместимым с Red Hat.
* Oracle UEK2 не поддерживается в Hyper-V и Azure, поскольку не включает необходимые драйверы.
* Формат VHDX не поддерживается в Azure, поддерживается только **фиксированный VHD**.  Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета convert-vhd.
* При установке системы Linux рекомендуется использовать стандартные разделы, а не LVM (как правило, значение по умолчанию во многих дистрибутивах). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок. Для дисков данных можно использовать [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Версии ядра Linux ниже 2.6.37 не поддерживают NUMA в Hyper-V с виртуальными машинами большего размера. Эта проблема влияет в основном на дистрибутивы более ранних версий, в которых используется вышестоящее ядро Red Hat 2.6.32, и была исправлена в Oracle Linux 6.6 и более поздних версий.
* Не настраивайте раздел подкачки на диске с ОС. Можно настроить агент Linux для создания файла подкачки на временном диске ресурсов.  Дополнительные сведения описаны далее.
* Размер виртуальной памяти всех VHD в Azure должен быть округлен до 1 МБ. При конвертации диска в формате RAW в виртуальный жесткий диск убедитесь, что размер диска RAW в несколько раз превышает 1 МБ. См. дополнительные сведения в [примечаниях по установке Linux](create-upload-generic.md#general-linux-installation-notes).
* Убедитесь, что `Addons` включен репозиторий. Измените файл `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) или `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux 7) и замените в нем строку `enabled=0` строкой `enabled=1` в разделе **[ol6_addons]** или **[ol7_addons]** .

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 и более поздние версии
Необходимо выполнить определенные действия с конфигурацией операционной системы, чтобы виртуальная машина запускалась в среде Azure.

1. На центральной панели диспетчера Hyper-V выберите виртуальную машину.
2. Щелкните **Подключение** , чтобы открыть окно виртуальной машины.
3. Удалите NetworkManager, выполнив следующую команду:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **Примечание.** Если пакет еще не установлен, эта команда завершится с сообщением об ошибке. Это ожидаемое поведение.
4. Создайте файл с именем **network** в каталоге `/etc/sysconfig/`. Файл должен содержать следующий текст:

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Создайте файл с именем **ifcfg-eth0** в каталоге `/etc/sysconfig/network-scripts/`. Файл должен содержать следующий текст:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Переместите или удалите правила udev, чтобы не создавать статические правила для интерфейса Ethernet. Эти правила приводят к появлению проблем при клонировании виртуальной машины в Microsoft Azure или Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

    ```console
    # chkconfig network on
    ```

8. Установите python-pyasn1, выполнив следующую команду:

    ```console
    # sudo yum install python-pyasn1
    ```

9. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл /boot/grub/menu.lst в текстовом редакторе и убедитесь в том, что ядро включает следующие параметры:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Это гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки.
   
   Помимо вышесказанного, рекомендуется *удалить* следующие параметры:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт.
   
   При желании можно настроить параметр `crashkernel` , однако учитывайте, что он сокращает объем доступной памяти в виртуальной машине на 128 МБ или более, что может оказаться проблемой в виртуальных машинах небольшого размера.
10. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки.  Обычно это сделано по умолчанию.
11. Установите агент Linux для Azure, выполнив следующую команду. Последней является версия 2.0.15.

    ```console
    # sudo yum install WALinuxAgent
    ```

    Обратите внимание, что установка пакета WALinuxAgent приведет к удалению пакетов NetworkManager и NetworkManager-gnome, если они еще не были удалены, как описано в шаге 2.
12. Не создавайте пространство подкачки на диске с ОС.
    
    Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Следует отметить, что локальный диск ресурсов является *временным* диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. В диспетчере Hyper-V выберите **Действие -> Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 и более поздние версии
**Изменения в Oracle Linux 7**

Подготовка виртуальной машины Oracle Linux 7 для Azure в значительной степени аналогична версии Oracle Linux 6, однако есть несколько важных отличий:

* Azure поддерживает Oracle Linux с ядром Unbreakable Enterprise Kernel (UEK) или ядром, совместимым с Red Hat. Рекомендуется использовать Oracle Linux с UEK.
* Пакет NetworkManager больше не конфликтует с агентом Azure Linux. Этот пакет устанавливается по умолчанию, и мы рекомендуем не удалять его.
* В качестве загрузчика по умолчанию теперь используется GRUB2, поэтому изменилась процедура правки параметров ядра (см. ниже).
* XFS теперь является файловой системой по умолчанию. При желании можно продолжать использование файловой системы ext4.

**Шаги настройки**

1. В диспетчере Hyper-V выберите виртуальную машину.
2. Щелкните **Подключение** , чтобы открыть окно консоли для виртуальной машины.
3. Создайте файл с именем **network** в каталоге `/etc/sysconfig/`. Файл должен содержать следующий текст:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Создайте файл с именем **ifcfg-eth0** в каталоге `/etc/sysconfig/network-scripts/`. Файл должен содержать следующий текст:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. Переместите или удалите правила udev, чтобы не создавать статические правила для интерфейса Ethernet. Эти правила приводят к появлению проблем при клонировании виртуальной машины в Microsoft Azure или Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

    ```console
    # sudo chkconfig network on
    ```

7. Установите пакет python-pyasn1, выполнив следующую команду:

    ```console
    # sudo yum install python-pyasn1
    ```

8. Выполните следующую команду, чтобы очистить текущие метаданные yum и установить все обновления:

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл /etc/default/grub в текстовом редакторе и измените параметр `GRUB_CMDLINE_LINUX`, например:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. При этом также отключаются соглашения об именовании сетевых карт в Oracle Linux 7 с Unbreakable Enterprise Kernel. Помимо вышесказанного, рекомендуется *удалить* следующие параметры:

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт.
   
   При желании можно настроить параметр `crashkernel` , однако учитывайте, что он сокращает объем доступной памяти в виртуальной машине на 128 МБ или более, что может оказаться проблемой в виртуальных машинах небольшого размера.
10. После внесения изменений в файл "/etc/default/grub", как указано выше, выполните следующую команду для повторного создания конфигурации grub:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки.  Обычно это сделано по умолчанию.
12. Установите агент Linux для Azure, выполнив следующую команду:

    ```console
    # sudo yum install WALinuxAgent
    # sudo systemctl enable waagent
    ```

13. Не создавайте пространство подкачки на диске с ОС.
    
    Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Следует отметить, что локальный диск ресурсов является *временным* диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом.

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

14. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:
    
    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

15. В диспетчере Hyper-V выберите **Действие -> Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.

## <a name="next-steps"></a>Дальнейшие действия
Теперь вы можете создавать новые виртуальные машины в Azure с помощью VHD-файла системы Oracle Linux. Если вы отправляете VHD-файл в Azure впервые, см. раздел [Вариант 1. Передача VHD](upload-vhd.md#option-1-upload-a-vhd).