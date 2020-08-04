---
title: Настройка и оптимизация виртуальных машин Azure серии H и серий N с поддержкой InfiniBand
description: Узнайте, как настроить и оптимизировать виртуальные машины серии H и N с поддержкой InfiniBand для HPC.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: c49286f370691c39c3d14d589f2657d6e0bb3c04
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542337"
---
# <a name="configure-and-optimize-vms"></a>Настройка и оптимизация виртуальных машин

В этой статье приведены известные методы настройки и оптимизации виртуальных машин серии [H](../../sizes-hpc.md) и [N](../../sizes-gpu.md) с поддержкой InfiniBand для HPC.

## <a name="vm-images"></a>Образы виртуальных машин
На виртуальных машинах с поддержкой InfiniBand для включения RDMA требуются соответствующие драйверы. В Linux образы виртуальных машин CentOS-HPC в Marketplace предварительно настроены с соответствующими драйверами. Образы виртуальных машин Ubuntu можно настроить с помощью правильных драйверов, выполнив приведенные [здесь инструкции](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351). Также рекомендуется создавать [пользовательские образы виртуальных машин](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images) с соответствующими драйверами и конфигурацией и повторно использовать их.

### <a name="centos-hpc-vm-images"></a>CentOS — образы виртуальных машин HPC
Для [виртуальных машин с поддержкой RDMA](../../sizes-hpc.md#rdma-capable-instances), не поддерживающих SR-IOV, CENTOS-HPC версии 6,5 или более поздней версии, подходит до 7,5 в Marketplace. Например, для [виртуальных машин серии H16](../../h-series.md)рекомендуется использовать версии 7,1 и 7,5. Эти образы виртуальных машин предварительно загружаются с драйверами сети Direct для RDMA и Intel MPI версии 5,1.

  Для [виртуальных машин с поддержкой RDMA](../../sizes-hpc.md#rdma-capable-instances), поддерживающих SR-IOV, подходят [CentOS-HPC версии 7,6 или более ПОЗДНИХ](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) версий виртуальных машин в Marketplace. Эти образы виртуальных машин оптимизированы и предварительно загружены с драйверами ОФЕД для RDMA и различными часто используемыми библиотеками MPI и инженерными пакетами для научных вычислений и являются самым простым способом приступить к работе.

  Примеры сценариев, используемых при создании образов виртуальных машин CentOS-HPC версии 7,6 и более поздних версий из базового образа CentOS Marketplace, находятся в [репозитории азпк-Images](https://github.com/Azure/azhpc-images/tree/master/centos).

### <a name="rhelcentos-vm-images"></a>Образы виртуальных машин RHEL/CentOS
Образы виртуальных машин на основе RHEL или CentOS, которые не поддерживают HPC в Marketplace, можно настроить для использования на [виртуальных машинах](../../sizes-hpc.md#rdma-capable-instances)с поддержкой RDMA с поддержкой SR-IOV. Узнайте больше о [включении InfiniBand](enable-infiniband.md) и [настройке MPI](setup-mpi.md) на виртуальных машинах.

  Примеры сценариев, используемых при создании образов виртуальных машин CentOS-HPC версии 7,6 и более поздних версий из базового образа CentOS Marketplace, находятся в [репозитории азпк-Images](https://github.com/Azure/azhpc-images/tree/master/centos).

### <a name="ubuntu-vm-images"></a>Образы виртуальных машин Ubuntu
Машины Ubuntu Server 16,04 LTS, 18,04 LTS и 20,04 LTS образы виртуальных машин в Marketplace поддерживаются для [виртуальных машин с поддержкой RDMA](../../sizes-hpc.md#rdma-capable-instances)(SR-IOV и без SR-IOV). Узнайте больше о [включении InfiniBand](enable-infiniband.md) и [настройке MPI](setup-mpi.md) на виртуальных машинах.

  Примеры сценариев, которые могут использоваться при создании образов виртуальных машин HPC на базе Ubuntu 18,04 LTS, находятся в [репозитории азпк-Images](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc).

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server образы виртуальных машин
SLES 12 SP3 для HPC, SLES 12 SP3 для HPC (Premium), SLES 12 SP1 для HPC, SLES 12 SP1 для HPC (Premium), SLES 12 SP4 и SLES 15 образов виртуальных машин в Marketplace поддерживаются. Эти образы виртуальных машин предварительно загружаются с драйверами сети Direct для RDMA и Intel MPI версии 5,1. Дополнительные сведения о [настройке MPI](setup-mpi.md) на виртуальных машинах.

## <a name="optimize-vms"></a>Оптимизация виртуальных машин

Ниже приведены некоторые дополнительные параметры оптимизации для повышения производительности виртуальной машины.

### <a name="update-lis"></a>Обновить LIS

Если это необходимо для функциональности или производительности, [драйверы Linux Integration Services (LIS)](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) могут быть установлены или обновлены на поддерживаемых ОС дистрибутивов, особенно при развертывании с помощью пользовательского образа или более старой версии ОС, например CENTOS/RHEL 6. x или более ранней версии 7. x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Освобождение памяти

Повышение производительности за счет автоматического освобождения памяти для предотвращения удаленного доступа к памяти.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Чтобы сохранить это после перезагрузки виртуальной машины, выполните следующие действия.

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Отключение брандмауэра и SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Отключить кпуповер

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>Настройка WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
При необходимости WALinuxAgent может быть отключен как шаг перед заданием и включен назад после задания для максимальной доступности ресурсов виртуальной машины для рабочей нагрузки HPC.


## <a name="next-steps"></a>Следующие шаги

- Узнайте больше о [включении InfiniBand](enable-infiniband.md) на виртуальных машинах серии [H](../../sizes-hpc.md) и [N](../../sizes-gpu.md) с поддержкой InfiniBand.
- Узнайте больше об установке различных [поддерживаемых библиотек MPI](setup-mpi.md) и их оптимальной конфигурации на виртуальных машинах.
- Ознакомьтесь с обзором [ХБ](hb-series-overview.md) и [описанием серии HC](hc-series-overview.md) , чтобы узнать о оптимальной настройке рабочих нагрузок для повышения производительности и масштабируемости.
- Ознакомьтесь с последними объявлениями и некоторыми примерами HPC и результатами в [блогах сообщества разработчиков Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Для более высокого уровня архитектуры выполнения рабочих нагрузок HPC см. статью [высокопроизводительные вычисления (HPC) в Azure](/azure/architecture/topics/high-performance-computing/).
