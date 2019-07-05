---
title: Известные проблемы с HB-HC-виртуальных машин серии и - виртуальных машинах Azure | Документация Майкрософт
description: Дополнительные сведения об известных проблемах с размерами виртуальных Машин серии HB в Azure.
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
ms.openlocfilehash: a41155b90257f7eaec85c3adbd975a0a37e24d91
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560419"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Известные проблемы с виртуальными машинами серий HB и HC

В этой статье предоставляет наиболее распространенные проблемы и решения, при использовании HB-HC-виртуальных машин серии и.

## <a name="dram-on-hb-series"></a>DRAM на серии HB

Виртуальные машины серии HB можно предоставлять только 228 ГБ ОЗУ для гостевых виртуальных машин в данный момент. Это из-за известное ограничение низкоуровневая оболочка Azure позволяет предотвратить страниц, назначаемый локального DRAM из AMD CCX элемента (домены NUMA) зарезервировано для гостевой виртуальной Машине.

## <a name="accelerated-networking"></a>Ускорение работы в сети

Повышение производительности сети Azure в настоящее время не включена, но будет по мере прохождения предварительной. Мы уведомим клиентов, когда эта функция поддерживается.

## <a name="qp0-access-restriction"></a>Ограничение доступа qp0

Для предотвращения доступа оборудования на низком уровне, который может привести к уязвимости системы безопасности, пары очередей 0 не доступен для гостевых виртуальных машин. Это должно влияют только на действия, обычно связанных с администрированием ConnectX-5 сетевого адаптера и выполняется диагностика некоторые InfiniBand, например ibdiagnet, но не сами приложения конечного пользователя.

## <a name="ud-transport"></a>Домен обновления транспорта

При запуске серии HB и гибридного подключения не поддерживают динамическое подключение транспорта (DCT-ОКНА). Со временем будет реализована поддержка DCT-ОКНА. Поддерживаются надежные транспортов подключения (RC) и ненадежными датаграмм (UD).

## <a name="gss-proxy"></a>GSS прокси-сервера

GSS прокси-сервера есть известная ошибка в CentOS/RHEL 7.5, могут проявляться в виде значительного увеличения производительности и снижения скорости реагирования при использовании с NFS. Это может быть смягчена с помощью:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Очистка кэша

В системах HPC часто полезно для очистки памяти, после завершения задания перед следующий пользователь назначается на том же узле. После запуска приложения в Linux вы можете обнаружить, что ваш доступной памяти сокращает при увеличения памяти буфера, несмотря на то, не смогут выполнять любые приложения.

![Снимок экрана: Командная строка](./media/known-issues/cache-cleaning-1.png)

С помощью `numactl -H` показано, какие NUMAnode(s) память буферизуется (возможно, все). В Linux, пользователи могут очистить кэши в три способа возвращения буфер или кэшированных памяти для «освобождения». Необходимо быть корневой или иметь разрешения sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Снимок экрана: Командная строка](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Предупреждения ядра

Может появиться следующие сообщения ядра предупреждение при загрузке виртуальной Машины серии HB под Linux.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

Это предупреждение можно игнорировать. Это вызвано известное ограничение низкоуровневая оболочка Azure, которая будет решена со временем.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [высокопроизводительные вычислительные системы](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) в Azure.
