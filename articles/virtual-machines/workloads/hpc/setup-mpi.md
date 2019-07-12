---
title: Установка Message Passing Interface для HPC - виртуальных машинах Azure | Документация Майкрософт
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
ms.openlocfilehash: 541e42a72ea604c4d71dc546b14dea2f0857bcc1
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797515"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Установка Message Passing Interface для HPC

Рабочие нагрузки интерфейса Передачи сообщений сообщения: значительную часть традиционных рабочих нагрузок HPC. Параметры SR-IOV включена размеры виртуальных Машин в Azure позволяют почти любой разновидности MPI для использования. 

Выполнения заданий MPI на виртуальных машинах нуждается в настройке ключей разделов (p ключи) для клиента. Выполните действия, описанные в [обнаружение ключи секций](#discover-partition-keys) Дополнительные сведения об определении значений p ключ.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) обеспечивает наилучшую производительность для IB происходит с MPICH и OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Установите UCX как описано выше.

```bash
sudo yum install –y openmpi
```

Создавайте OpenMPI.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Запустите OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Как упоминалось выше, проверьте ключ секции.

## <a name="mpich"></a>MPICH

Установите UCX как описано выше.

Создавайте MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

Под управлением MPICH.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Как упоминалось выше, проверьте ключ секции.

## <a name="mvapich2"></a>MVAPICH2

Создавайте MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Под управлением MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Платформа MPI Community Edition

Установка необходимых пакетов для платформы MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Выполните процесс установки.

## <a name="intel-mpi"></a>Intel MPI

[Скачать Intel MPI](https://software.intel.com/mpi-library/choose-download).

Изменение переменной среды I_MPI_FABRICS зависимости от версии. Для Intel MPI 2018, используйте `I_MPI_FABRICS=shm:ofa` и 2019 г., используйте `I_MPI_FABRICS=shm:ofi`.

Закрепление процесс работает правильно для 15, 30 и 60 PPN по умолчанию.

## <a name="osu-mpi-benchmarks"></a>Тест производительности OSU MPI

[Загрузить тесты производительности MPI OSU](http://mvapich.cse.ohio-state.edu/benchmarks/) и распаковать двоичный.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Создание тестов производительности с помощью определенную библиотеку MPI:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

Тесты производительности MPI находятся под `mpi/` папки.


## <a name="discover-partition-keys"></a>Обнаружение ключи секций

Получить ключи раздела (p ключи) для взаимодействия с другими виртуальными машинами в одном клиенте (группы доступности или масштабируемом наборе виртуальных Машин).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Больший из них является ключом клиента, который должен использоваться с MPI. Пример: Если p ключи, 0x800b следует использовать с MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Используйте раздел, отличный от ключа раздела по умолчанию (0x7fff). UCX требует MSB p-ключа для очистки. Например в качестве UCX_IB_PKEY 0x000b для 0x800b.

Обратите внимание на то, что до тех пор, пока существует клиента (AVSet или масштабируемого набора виртуальных МАШИН), PKEYs не изменяются. Это верно даже в том случае, когда узлы будут добавлены или удалены. Новые клиенты получить другой PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Настройка максимального числа пользователей для MPI

Настройка максимального числа пользователей для MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Настройка ключей SSH для MPI

Настройка ключей SSH для типов MPI, которые ее требуют.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

Синтаксисе выше предполагается, что общий домашний каталог, else каталоге .ssh должны быть скопированы на каждый узел.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) в Azure.
