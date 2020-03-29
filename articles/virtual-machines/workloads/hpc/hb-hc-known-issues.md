---
title: Известные проблемы с HB-серии и HC-серии VMs - Azure Виртуальные машины (ru) Документы Майкрософт
description: Узнайте об известных проблемах с размерами VM серии HB в Azure.
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
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707782"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Известные проблемы с виртуальными машинами серий HB и HC

В этой статье приводятся наиболее распространенные проблемы и решения при использовании HB-серии и HC-серии VMs.

## <a name="dram-on-hb-series"></a>DRAM на HB-серии

VMs-серии HB могут выставлять только 228 ГБ оперативной памяти для приглашенных ВМ в это время. Это связано с известным ограничением гипервизора Azure, чтобы предотвратить присвоение страниц локальному DRAM доменов AMD CCX (NUMA), зарезервированным для гостевого VM.

## <a name="accelerated-networking"></a>Ускорение работы в сети

Ускоренная сеть Azure не включена в данный момент, но будет, как мы прогрессчерез период предварительного просмотра. Мы уведомим клиентов, когда эта функция будет поддержана.

## <a name="qp0-access-restriction"></a>Ограничение доступа qp0

Для предотвращения низкого уровня аппаратного доступа, который может привести к уязвимостям безопасности, очередь пара 0 недоступна для гостей VMs. Это должно повлиять только на действия, обычно связанные с администрированием NIC ConnectX-5, и запуск некоторых диагностик InfiniBand, таких как ibdiagnet, но не сами конечные пользователи приложений.

## <a name="ud-transport"></a>UD Транспорт

При запуске hB- и HC-серии не поддерживают динамически подключенный транспорт (DCT). Поддержка DCT будет осуществляться с течением времени. Надежное подключение (RC) и ненадежные datagram (UD) транспорты поддерживаются.

## <a name="gss-proxy"></a>Прокси-сервер GsS

GSS Proxy имеет известную ошибку в CentOS/RHEL 7.5, которая может проявляться как значительная производительность и ответственность штрафа при использовании с NFS. Это можно смягчить с помощью:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Очистка кэша

В системах HPC часто полезно очистить память после завершения задания до того, как следующему пользователю присваивается тот же узел. После запуска приложений в Linux вы можете обнаружить, что доступная память уменьшается, в то время как память буфера увеличивается, несмотря на то, что не работает никаких приложений.

![Скриншот запроса команды](./media/known-issues/cache-cleaning-1.png)

Использование `numactl -H` покажет, какой NUMAnode (ы) память буферизирована с (возможно, все). В Linux пользователи могут очищать кэши тремя способами, чтобы вернуть буферизированную или кэшированную память на «свободную». Вы должны быть корень или sudo разрешений.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Скриншот запроса команды](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Предупреждения о ядрах

Вы можете увидеть следующие предупреждающие сообщения ядра при загрузке VM серии HB под Linux.

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

Вы можете проигнорировать это предупреждение. Это связано с известным ограничением гипервизора Azure, который будет решаться с течением времени.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [высокопроизводительных вычислениях](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) в Azure.
