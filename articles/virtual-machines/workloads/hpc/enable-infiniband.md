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
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196761"
---
# <a name="enable-infiniband-with-sr-iov"></a>Включение InfiniBand с помощью SR-IOV

Виртуальные машины Azure NC, ND и H поддерживаются в выделенной сети InfiniBand. Все размеры с поддержкой RDMA могут использовать эту сеть с помощью Intel MPI. В некоторых рядах виртуальных машин расширена поддержка всех реализаций MPI и команд RDMA через SR-IOV. Виртуальные машины с поддержкой RDMA включают в себя [оптимизированные GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) и [высокопроизводительные вычислительные системы (HPC)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) .

## <a name="choose-your-installation-path"></a>Выбор пути установки

Чтобы приступить к работе, самый простой вариант — использовать образ платформы, предварительно настроенный для InfiniBand, если он доступен:

- **Виртуальные машины IaaS для HPC** . чтобы приступить к работе с виртуальными машинами IaaS для HPC, простейшим решением будет использование [образа ОС виртуальной машины CentOS-HPC 7,6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), который уже настроен с помощью InfiniBand. Так как этот образ уже настроен с помощью InfiniBand, настраивать его вручную не нужно. Сведения о совместимых версиях Windows см. в разделе [экземпляры, поддерживающие Windows RDMA](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

- **Виртуальные машины IaaS GPU** — образы платформы в настоящее время не предварительно настроены для виртуальных машин, оптимизированных для GPU, за исключением [образа ОС CentOS-HPC 7,6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Сведения о настройке пользовательского образа с помощью InfiniBand см. в [статье Установка MELLANOX Офед вручную](#manually-install-mellanox-ofed).

Если вы используете пользовательский образ виртуальной машины или виртуальную машину, [оптимизированную для GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) , необходимо настроить ее с помощью модуля InfiniBand, добавив расширение виртуальной машины Инфинибанддриверлинукс или инфинибанддривервиндовс в развертывание. Узнайте, как использовать эти расширения виртуальных машин с [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) и [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Установка Mellanox ОФЕД вручную

Чтобы вручную настроить InfiniBand с помощью SR-IOV, выполните следующие действия. В примере на этих шагах показан синтаксис для RHEL/CentOS, но эти действия являются общими и могут использоваться для любой совместимой операционной системы, такой как Ubuntu (16,04, 18,04 19,04) и SLES (12 SP4 и 15). Драйверы для папки "Входящие" также работают, но драйверы Mellanox Опенфабрикс предоставляют больше возможностей.

Дополнительные сведения о поддерживаемых дистрибутивах для драйвера Mellanox см. в последних [драйверах Mellanox опенфабрикс](https://www.mellanox.com/page/products_dyn?product_family=26). Дополнительные сведения о драйвере Mellanox Опенфабрикс см. в разделе с [руководством пользователя Mellanox](https://docs.mellanox.com/category/mlnxofedib).

Сведения о настройке InfiniBand в Linux см. в следующем примере:

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

Для Windows Скачайте и установите [драйверы MELLANOX офед для Windows](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Включить IP-адрес через InfiniBand

Используйте следующие команды, чтобы включить IP-адрес через InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) в Azure.
