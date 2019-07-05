---
title: Поддержка высокопроизводительных вычислительных систем — виртуальные машины Azure | Документация Майкрософт
description: Дополнительные сведения о высокопроизводительных вычислительных систем в Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: e8ff4147130dfeff14be41ed292b51ed34966df0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537683"
---
# <a name="optimization-for-linux"></a>Оптимизация Linux

В этой статье показано несколько методик для оптимизации образа ОС. Дополнительные сведения о [Включение InfiniBand](enable-infiniband.md) и оптимизации образов ОС.

## <a name="update-lis"></a>Обновить локальный источник установки

Если развертывание с помощью пользовательского образа (например, более старых ОС, таких как CentOS/RHEL 7.4 или 7.5), обновите LIS на виртуальной Машине.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Освободить память

Повысить эффективность, автоматическую очистку памяти, чтобы избежать удаленный доступ к памяти.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Чтобы сделать это сохраняются после перезагрузки виртуальной Машины:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Отключить брандмауэр и SELinux

Отключите брандмауэр и SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Отключить cpupower

Отключите cpupower.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [Включение InfiniBand](enable-infiniband.md) и оптимизации образов ОС.

* Дополнительные сведения о [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) в Azure.
