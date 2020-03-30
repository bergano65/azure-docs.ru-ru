---
title: Настройка интерфейса передачи сообщений для HPC - Виртуальные машины Azure (ru) Документы Майкрософт
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
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023996"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Настройка интерфейса передачи сообщений для HPC

Рабочие нагрузки переходящего интерфейса сообщений (MPI) являются значительной частью традиционных рабочих нагрузок HPC. SR-IOV позволил размеры VM на Azure позволяют использовать практически любой вкус MPI. 

Запуск заданий MPI на ВМ требует настройки ключей раздела (p-keys) между арендатором. Выполните последующие действия в разделе [Discover в](#discover-partition-keys) разделе для получения подробной информации об определении значений p-key.

## <a name="ucx"></a>Ucx

[UCX](https://github.com/openucx/ucx) предлагает лучшие показатели на IB и работает с MPICH и OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Установите UCX, как описано ранее.

```bash
sudo yum install –y openmpi
```

Создайте OpenMPI.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Выполнить OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Проверьте ключ раздела, как упоминалось выше.

## <a name="mpich"></a>МПИЧ

Установите UCX, как описано ранее.

Постройте MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

Запуск MPICH.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Проверьте ключ раздела, как упоминалось выше.

## <a name="mvapich2"></a>MVAPICH2

Постройте MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Запуск MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Платформа MPI Community Edition

Установите необходимые пакеты для MpI платформы.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Следите за процессом установки.

## <a name="intel-mpi"></a>Intel MPI

[Скачать Intel MPI](https://software.intel.com/mpi-library/choose-download).

Измените переменную среды I_MPI_FABRICS в зависимости от версии. Для Intel MPI 2018, использование `I_MPI_FABRICS=shm:ofa` и на `I_MPI_FABRICS=shm:ofi`2019 год, использование .

Процесс закрепления работает правильно для 15, 30 и 60 PPN по умолчанию.

## <a name="osu-mpi-benchmarks"></a>Ориентиры ОГУ MPI

[Скачать OSU MPI benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) и untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Создавайте контрольные показатели с помощью определенной библиотеки MPI:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI Benchmarks `mpi/` находятся под папкой.


## <a name="discover-partition-keys"></a>Откройте для себя ключи раздела

Откройте для себя ключи раздела (p-keys) для общения с другими вводом в рамках того же арендатора (Set availability или VM Scale Set).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Чем больше из двух является арендатором ключ, который должен быть использован с MPI. Пример: Если ниже p-ключи, 0x800b следует использовать с MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Используйте ключ раздела, кроме ключа раздела по умолчанию (0x7fff). UCX требует, чтобы MSB p-key был очищен. Например, установить UCX_IB_PKEY как 0x000b для 0x800b.

Также обратите внимание, что до тех пор, как арендатор (AVSet или VMSS) существует, PKEYs остаются теми же. Это верно даже при добавлении/удалении узлов. Новые арендаторы получают различные PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Настройка пользовательских ограничений для MPI

Настройка пользовательских ограничений для MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Настройка SSH-ключей для MPI

Настройка SSH-ключей для типов MPI, которые требуют этого.

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

Вышеупомянутый синтаксис предполагает общий домашний каталог, иначе каталог .ssh должен быть скопирован на каждый узлы.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) на Azure.
