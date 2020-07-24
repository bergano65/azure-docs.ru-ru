---
title: Настройка интерфейса передачи сообщений для HPC — виртуальные машины Azure | Документация Майкрософт
description: Узнайте, как настроить MPI для HPC в Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 3ca9a21d105be6f17c1aa40ae1a0ab7f01c38184
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083418"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Настройка интерфейса передачи сообщений для HPC

Рабочие нагрузки интерфейса передачи сообщений (MPI) являются важной частью традиционных рабочих нагрузок HPC. Размеры виртуальных машин с поддержкой SR-IOV в Azure позволяют использовать почти любую разновидность MPI. 

Для выполнения заданий MPI на виртуальных машинах необходимо настроить ключи секций (p-ключи) в клиенте. Чтобы узнать о том, как определить значения p-ключа, выполните действия, описанные в разделе [Обнаружение ключей разделов](#discover-partition-keys) .

## <a name="ucx"></a>уккс

[Уккс](https://github.com/openucx/ucx) обеспечивает наилучшую производительность при работе с Мпич и опенмпи.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>опенмпи

Установите УККС, как описано выше.

```bash
sudo yum install –y openmpi
```

Сборка Опенмпи.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Запустите Опенмпи.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Проверьте ключ раздела, как упоминалось выше.

## <a name="mpich"></a>MPICH

Установите УККС, как описано выше.

Сборка МПИЧ.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

Выполнение МПИЧ.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Проверьте ключ раздела, как упоминалось выше.

## <a name="mvapich2"></a>MVAPICH2

Сборка MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Выполнение MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Платформа MPI Community Edition

Установите необходимые пакеты для платформы MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Выполните процедуру установки.

## <a name="intel-mpi"></a>Intel MPI

[Загрузите Intel MPI](https://software.intel.com/mpi-library/choose-download).

Измените переменную среды I_MPI_FABRICS в зависимости от версии. Для Intel MPI 2018 используйте, `I_MPI_FABRICS=shm:ofa` а для 2019 — `I_MPI_FABRICS=shm:ofi` .

По умолчанию закрепление процессов работает правильно для 15, 30 и 60 ППН.

## <a name="osu-mpi-benchmarks"></a>Тесты производительности осу MPI

[Скачайте тесты производительности осу MPI](http://mvapich.cse.ohio-state.edu/benchmarks/) и распаковать.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Создавайте тесты производительности с помощью определенной библиотеки MPI:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

Тесты MPI находятся в `mpi/` папке.


## <a name="discover-partition-keys"></a>Обнаружение ключей разделов

Обнаружение ключей разделов (p-Keys) для взаимодействия с другими виртуальными машинами в рамках одного клиента (группы доступности или масштабируемого набора ВМ).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Чем больше из двух, тем ключ клиента следует использовать с MPI. Пример. Если используются следующие ключи p-Keys, 0x800b следует использовать с MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Используйте раздел, отличный от ключа раздела по умолчанию (0x7FFF). УККС требует, чтобы был сброшен параметр p-Key. Например, задайте UCX_IB_PKEY как 0x000b для 0x800b.

Также обратите внимание, что пока существует клиент (AVSet или VMSS), PKEY остается неизменным. Это справедливо даже при добавлении или удалении узлов. Новые клиенты получают разные PKEY.


## <a name="set-up-user-limits-for-mpi"></a>Настройка ограничений пользователей для MPI

Настройте ограничения пользователей для MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Настройка ключей SSH для MPI

Настройте ключи SSH для требуемых типов MPI.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

В приведенном выше синтаксисе предполагается наличие общего домашнего каталога, иначе каталог. ssh должен быть скопирован на каждый узел.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [HPC](/azure/architecture/topics/high-performance-computing/) в Azure.
