---
title: Высокопроизводительные вычислительные системы — виртуальные машины Azure | Документация Майкрософт
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67707826"
---
# <a name="optimization-for-linux"></a>Оптимизация Linux

В этой статье показано несколько ключевых методов оптимизации образа операционной системы. Узнайте больше о [включении InfiniBand](enable-infiniband.md) и оптимизации образов ОС.

## <a name="update-lis"></a>Обновить LIS

При развертывании с помощью пользовательского образа (например, более старой ОС, например CentOS/RHEL 7,4 или 7,5), обновите LIS на виртуальной машине.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Освобождение памяти

Повышение эффективности за счет автоматического освобождения памяти для предотвращения удаленного доступа к памяти.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Чтобы сохранить это после перезагрузки виртуальной машины, выполните следующие действия.

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Отключение брандмауэра и SELinux

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

## <a name="disable-cpupower"></a>Отключить кпуповер

Отключите кпуповер.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Следующие шаги

* Узнайте больше о том, как [включить InfiniBand](enable-infiniband.md) и оптимизировать образы ОС.

* Дополнительные сведения о [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) в Azure.
