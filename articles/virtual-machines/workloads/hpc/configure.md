---
title: Высокопроизводительные вычисления - Виртуальные машины Azure Документы Майкрософт
description: Узнайте о высокопроизводительных вычислениях в Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707826"
---
# <a name="optimization-for-linux"></a>Оптимизация Linux

В этой статье показаны некоторые ключевые методы оптимизации изображения ОС. Узнайте больше о [включении InfiniBand](enable-infiniband.md) и оптимизации изображений ОС.

## <a name="update-lis"></a>Обновление LIS

При развертывании с помощью пользовательского изображения (например, более старой ОС, такой как CentOS/RHEL 7.4 или 7.5), обновите LIS на VM.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Восстановить память

Повышение эффективности путем автоматической рекультивации памяти, чтобы избежать удаленного доступа к памяти.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Чтобы сделать это сохранить после перезагрузки VM:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Отключить брандмауэр и SELinux

Отключить брандмауэр и SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Отключенная cpupower

Отключенная cpupower.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о [включении InfiniBand](enable-infiniband.md) и оптимизации изображений ОС.

* Узнайте больше о [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) на Azure.
