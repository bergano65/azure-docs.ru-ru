---
title: Включение Инифинбанд с помощью виртуальных машин Azure SR-IOV | Документация Майкрософт
description: Узнайте, как включить InfiniBand с помощью SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 7218fceae71969f204c6c25ba4793a7c94341693
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858481"
---
# <a name="enable-infiniband-with-sr-iov"></a>Включение InfiniBand с помощью SR-IOV

Самый простой и рекомендуемый способ приступить к работе с виртуальными машинами IaaS для HPC — использовать образ ОС виртуальной машины CentOS-HPC 7,6. Если вы используете пользовательский образ виртуальной машины, самый простой способ его настроить с помощью InfiniBand (превышено) — добавить в развертывание расширение виртуальной машины Инфинибанддриверлинукс или Инфинибанддривервиндовс.
Узнайте, как использовать эти расширения виртуальных машин с [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) и [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Чтобы вручную настроить InfiniBand на виртуальных машинах с поддержкой SR-IOV (в настоящее время ХБ и HC Series), выполните следующие действия. Эти действия предназначены только для RHEL/CentOS. Для Ubuntu (16,04 и 18,04) и SLES (12 SP4 и 15) драйверы папки "Входящие" работают правильно.

## <a name="manually-install-ofed"></a>Установка ОФЕД вручную

Установите последние версии драйверов MLNX_OFED для ConnectX-5 от [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26).

Для RHEL/CentOS (пример ниже для 7,6):

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Для Windows Скачайте и установите драйверы Виноф-2 для ConnectX-5 от [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34) .

## <a name="enable-ipoib"></a>Включение IPoIB

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>Назначение IP-адреса

Назначьте IP-адрес интерфейсу IB0, используя один из следующих способов:

- Вручную назначьте IP-адрес интерфейсу IB0 (в качестве корневого).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

ИЛИ

- Используйте WALinuxAgent, чтобы назначить IP-адрес и сделать его постоянным.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) в Azure.
