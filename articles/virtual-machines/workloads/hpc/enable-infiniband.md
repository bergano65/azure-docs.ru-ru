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
ms.openlocfilehash: de61403b62f80bea7872d5ab3561567ae2109590
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500074"
---
# <a name="enable-infiniband-with-sr-iov"></a>Включение InfiniBand с помощью SR-IOV

Виртуальные машины Azure NC, ND и H поддерживаются в выделенной сети InfiniBand. Все размеры с поддержкой RDMA могут использовать эту сеть с помощью Intel MPI. В некоторых рядах виртуальных машин расширена поддержка всех реализаций MPI и команд RDMA через SR-IOV. Виртуальные машины с поддержкой RDMA включают в себя [оптимизированные GPU](../../sizes-gpu.md) и [высокопроизводительные вычислительные системы (HPC)](../../sizes-hpc.md) .

## <a name="choose-your-installation-path"></a>Выбор пути установки

Чтобы приступить к работе, самый простой вариант — использовать образ платформы, предварительно настроенный для InfiniBand, если он доступен:

- **Виртуальные машины IaaS для HPC** . чтобы приступить к работе с виртуальными машинами IaaS для HPC, простейшим решением будет использование [образа ОС виртуальной машины CentOS-HPC 7,6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), который уже настроен с помощью InfiniBand. Так как этот образ уже настроен с помощью InfiniBand, настраивать его вручную не нужно. Сведения о совместимых версиях Windows см. в разделе [экземпляры, поддерживающие Windows RDMA](../../sizes-hpc.md#rdma-capable-instances).

- **Виртуальные машины IaaS GPU** — образы платформы в настоящее время не предварительно настроены для виртуальных машин, оптимизированных для GPU, за исключением [образа ОС CentOS-HPC 7,6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Сведения о настройке пользовательского образа с помощью InfiniBand см. в [статье Установка MELLANOX Офед вручную](#manually-install-mellanox-ofed).

Если вы используете пользовательский образ виртуальной машины или виртуальную машину, [оптимизированную для GPU](../../sizes-gpu.md) , необходимо настроить ее с помощью модуля InfiniBand, добавив расширение виртуальной машины Инфинибанддриверлинукс или инфинибанддривервиндовс в развертывание. Узнайте, как использовать эти расширения виртуальных машин с [Linux](../../sizes-hpc.md#rdma-capable-instances) и [Windows](../../sizes-hpc.md#rdma-capable-instances).

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [HPC](/azure/architecture/topics/high-performance-computing/) в Azure.
