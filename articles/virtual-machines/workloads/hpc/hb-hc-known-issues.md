---
title: Известные проблемы с виртуальными машинами серии ХБ и HC (виртуальные машины Azure) | Документация Майкрософт
description: Сведения об известных проблемах с размерами виртуальных машин серии ХБ в Azure.
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
ms.openlocfilehash: e85ae50321b9aa034f6a6d2cadcc329a24dafa62
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500024"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Известные проблемы с виртуальными машинами серий HB и HC

Эта статья содержит наиболее распространенные проблемы и решения при использовании виртуальных машин серии ХБ и HC.

## <a name="dram-on-hb-series"></a>DRAM в серии ХБ

Виртуальные машины серии ХБ в настоящее время могут предоставлять в Гостевые виртуальные машины только 228 ГБ ОЗУ. Это связано с известным ограничением низкоуровневой оболочки Azure, чтобы предотвратить назначение страниц локальной памяти DRAM для виртуальных машин AMD КККС (в доменах NUMA), зарезервированных для гостевой виртуальной машины.

## <a name="accelerated-networking"></a>Ускорение работы в сети

Служба "Ускоренная работа в Azure" в настоящее время не включена, но будет по мере прохождения через период предварительной версии. Мы будем уведомлять клиентов о том, что эта функция поддерживается.

## <a name="qp0-access-restriction"></a>Ограничение доступа qp0

Чтобы предотвратить доступ к оборудованию низкого уровня, что может привести к уязвимостям безопасности, пара очередей 0 недоступна для гостевых виртуальных машин. Это должно влиять только на действия, обычно связанные с администрированием сетевого адаптера ConnectX-5, и на выполнение некоторых диагностических проверок InfiniBand, таких как ибдиагнет, но не сами приложения конечных пользователей.

## <a name="ud-transport"></a>Транспорт обновления

При запуске серии ХБ-and HC не поддерживают динамически подключенный транспорт (ДКТ). Поддержка ДКТ будет реализована с течением времени. Поддерживаются транспорты с надежным подключением (RC) и ненадежными датаграммами (обновления).

## <a name="gss-proxy"></a>Прокси-сервер GSS

В прокси-сервере GSS имеется известная ошибка в CentOS/RHEL 7,5, которая может переявляться при использовании NFS в качестве значительного снижения производительности и скорости реагирования. Это можно устранить с помощью следующих действий.

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Очистка кэша

В системах HPC часто бывает полезно очистить память после завершения задания, прежде чем следующему пользователю будет назначен тот же узел. После запуска приложений в Linux может оказаться, что объем доступной памяти сокращается при увеличении памяти буфера, несмотря на то, что не выполняются никакие приложения.

![Снимок экрана командной строки](./media/known-issues/cache-cleaning-1.png)

`numactl -H`При использовании будет показано, какие NUMAnode памяти буферизованы (возможно, все). В Linux пользователи могут очищать кэши тремя способами, чтобы вернуть буферную или кэшированную память в "Free". Необходимо быть корневым или иметь разрешения sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Снимок экрана командной строки](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Предупреждения ядра

При загрузке виртуальной машины серии ХБ в Linux могут появиться следующие предупреждающие сообщения ядра.

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

Это предупреждение можно игнорировать. Это связано с известным ограничением низкоуровневой оболочки Azure, которое будет решаться с течением времени.

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о [высокопроизводительных вычислениях](/azure/architecture/topics/high-performance-computing/) в Azure.
