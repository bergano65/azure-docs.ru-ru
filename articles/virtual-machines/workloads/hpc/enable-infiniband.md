---
title: Включить InifinBand с SR-IOV - виртуальных машин Azure | Документация Майкрософт
description: Сведения о включении InfiniBand с SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 879b1eed7bf4778d4d49f6f991d6d74214d33823
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537658"
---
# <a name="enable-infiniband-with-sr-iov"></a>Включить InfiniBand с SR-IOV


— Это самый простой и рекомендуемый способ настройки вашего пользовательского образа виртуальной Машины с помощью InfiniBand (IB) для добавления расширения InfiniBandDriverLinux или InfiniBandDriverWindows виртуальной Машины к развертыванию.
Сведения об использовании этих расширений виртуальной Машины с [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) и [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Чтобы вручную настроить InfiniBand на SR-IOV включена виртуальными машинами (в настоящее время HB и HC), выполните следующие действия. Эти действия предназначены только для RHEL и CentOS. Для Ubuntu (16.04 и 18.04) и SLES (12 SP4 и 15) драйверы работают хорошо. Для Ubuntu. 


## <a name="manually-install-ofed"></a>Вручную установите OFED

Установите последние версии драйверов MLNX_OFED ConnectX-5 из [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26).

Для RHEL и CentOS (7.6 пример ниже):
```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Для Windows, загрузите и установите драйверы WinOF-2 для ConnectX – 5 из [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ipoib"></a>Включить IPoIB

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>Назначение IP-адресом

Назначьте IP-адрес интерфейсу ib0, с помощью:

- Вручную назначьте IP-адрес интерфейса ib0 (в качестве привилегированного пользователя).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

Или

- Воспользуйтесь WALinuxAgent для назначения IP-адреса и его создания.

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) в Azure.
