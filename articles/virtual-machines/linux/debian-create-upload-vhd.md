---
title: Подготовка виртуального жесткого диска Debian Linux в Azure | Документация Майкрософт
description: Дополнительные сведения о создании VHD-образов Debian для развертывания в Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: szark
ms.openlocfilehash: 6ef0a9f6efbf5f8398ba242150b2eb6102875f7e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58000325"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Подготовка виртуального жесткого диска Debian для Azure
## <a name="prerequisites"></a>Технические условия
В этом разделе предполагается, что вы уже установили на виртуальный жесткий диск (VHD) операционную систему Debian Linux из ISO-файла, полученного с [веб-сайта Debian](https://www.debian.org/distrib/). Существует несколько средств для создания VHD-файлов. Hyper-V — это лишь один из примеров. Инструкции по работе с Hyper-V см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Замечания по установке
* См. дополнительные сведения о [подготовке Linux для Azure](create-upload-generic.md#general-linux-installation-notes).
* Более новый формат VHDX не поддерживается в Azure. Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета **convert-vhd** .
* При установке Linux рекомендуется использовать стандартные разделы, а не LVM — значение по умолчанию во многих дистрибутивах. Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок. Для дисков данных можно использовать [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Не настраивайте раздел подкачки на диске с ОС. Можно настроить агент Linux для Azure для создания файла подкачки на временном диске ресурсов. Дополнительные сведения можно найти в инструкциях ниже.
* Размер виртуальной памяти всех VHD в Azure должен быть округлен до 1 МБ. При конвертации неподготовленного диска в формат VHD убедитесь, что размер этого диска в несколько раз превышает 1 МБ. См. дополнительные сведения об [ установке Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Создание виртуальных жестких дисков Debian с помощью Azure-Manage
Создавать виртуальные жесткие диски Debian для Azure можно с помощью скриптов [azure-manage](https://github.com/credativ/azure-manage) от [Credativ](https://www.credativ.com/). Рекомендуется использовать их, а не создавать образ с нуля. Например, чтобы создать виртуальный жесткий диск Debian 8, выполните следующие команды для скачивания средства `azure-manage` (и зависимостей) и выполнения скрипта `azure_build_image`:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Подготовка виртуального жесткого диска Debian вручную
1. В диспетчере Hyper-V выберите виртуальную машину.
2. Щелкните **Подключение** , чтобы открыть окно консоли для виртуальной машины.
3. Если вы установили ОС с помощью ISO-файла, закомментируйте все строки, относящиеся к `deb cdrom`в `/etc/apt/source.list`.

4. Отредактируйте файл `/etc/default/grub` и измените параметр **GRUB_CMDLINE_LINUX** следующим образом, чтобы включить дополнительные параметры ядра для Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Перестройте grub и выполните команду:

        # sudo update-grub

6. Добавьте репозитории Azure Debian в файл /etc/apt/sources.list для Debian 8 или 9:

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Stretch"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Установите агент Linux для Azure:
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. Для версий Debian 9+ рекомендуется использовать новое облачное ядро Debian для использования с виртуальными машинами в Azure. Чтобы установить это ядро, сначала создайте файл с именем /etc/apt/preferences.d/linux.pref со следующим содержимым:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Затем выполните команду sudo apt-get install linux-image-cloud-amd64, чтобы установить новое облачное ядро Debian.

9. Отмените подготовку виртуальной машины, подготовьте ее к работе в среде Azure и выполните команду:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. В диспетчере Hyper-V выберите **Действие** -> Завершение работы. Виртуальный жесткий диск Linux готов к передаче в Azure.

## <a name="next-steps"></a>Дальнейшие действия
Теперь виртуальный жесткий диск Debian можно использовать для создания новых виртуальных машин Azure. Если вы отправляете VHD-файл в Azure впервые, см. раздел [Вариант 1. Передача VHD](upload-vhd.md#option-1-upload-a-vhd).

