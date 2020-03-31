---
title: Включить InifinBand с SR-IOV - Azure Виртуальные машины (ru) Документы Майкрософт
description: Узнайте, как включить InfiniBand с SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196761"
---
# <a name="enable-infiniband-with-sr-iov"></a>Включить InfiniBand с SR-IOV

Azure NC, ND и H-серии VMs поддерживаются выделенной сетью InfiniBand. Все размеры с поддержкой RDMA способны использовать эту сеть с помощью MPI Intel. Некоторые серии VM расширили поддержку всех реализаций MPI и глаголов RDMA через SR-IOV. RDMA способны ХМ включают [GPU оптимизированы](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) и [высокопроизводительные вычисления (HPC)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) VMs.

## <a name="choose-your-installation-path"></a>Выберите траекторию установки

Для начала, самый простой вариант заключается в использовании изображения платформы предварительно настроены для InfiniBand, где это возможно:

- **HPC IaaS VMs** - Чтобы начать работу с IaaS VMs для HPC, самым простым решением является использование [изображения CentOS-HPC 7.6 VM OS](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), которое уже настроено с InfiniBand. Поскольку это изображение уже настроено с Помощью InfiniBand, вам не нужно настраивать его вручную. Для совместимых версий Windows смотрите [экземпляры, способные к Windows RDMA.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

- **GPU IaaS VMs** - Нет платформы изображения в настоящее время предварительно настроены для GPU оптимизированных VMs, за исключением [CentOS-HPC 7.6 VM OS изображения](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Чтобы настроить пользовательское изображение с помощью InfiniBand, [см.](#manually-install-mellanox-ofed)

Если вы используете пользовательское изображение VM или [GPU оптимизированный](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) VM, вы должны настроить его с InfiniBand, добавив InfiniBandDriverLinux или InfiniBandDriverWindows VM расширение для развертывания. Узнайте, как использовать эти расширения VM с [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) и [Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

## <a name="manually-install-mellanox-ofed"></a>Ручная установка Mellanox OFED

Чтобы вручную настроить InfiniBand с SR-IOV, используйте следующие шаги. Пример в этих шагах показывает синтаксис для RHEL/CentOS, но шаги являются общими и могут быть использованы для любой совместимой операционной системы, такой как Ubuntu (16.04, 18.04 19.04) и SLES (12 SP4 и 15). Драйверы папок "Входящие" также работают, но драйверы Mellanox OpenFabrics предоставляют больше возможностей.

Для получения дополнительной информации о поддерживаемых дистрибутивах для водителя Mellanox, [см.](https://www.mellanox.com/page/products_dyn?product_family=26) Для получения дополнительной информации о драйвере Mellanox OpenFabrics, см. [Mellanox user guide](https://docs.mellanox.com/category/mlnxofedib)

Смотрите следующий пример настройки InfiniBand на Linux:

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

Для Windows, скачать и установить [Mellanox OFED для драйверов Windows.](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ip-over-infiniband"></a>Включить IP над InfiniBand

Используйте следующие команды, чтобы включить IP через InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) на Azure.
