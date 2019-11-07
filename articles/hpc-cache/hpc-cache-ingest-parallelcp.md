---
title: Скрипт приема данных кэша Azure HPC для параллельного копирования
description: Как использовать скрипт параллельного копирования для перемещения данных в целевое хранилище BLOB-объектов в кэше HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 0bb74dcd683145fbae22cf0b6d2827ad9e16de0e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582692"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Метод создания скрипта копирования данных кэша HPC Azure

В этой статье приводятся инструкции по созданию сценария ``parallelcp`` и его использованию для перемещения данных в контейнер хранилища BLOB-объектов для использования с кэшем HPC Azure.

Дополнительные сведения о перемещении данных в хранилище BLOB-объектов для кэша HPC Azure см. в статье [Перемещение данных в хранилище BLOB-объектов Azure для кэша HPC Azure](hpc-cache-ingest.md).

## <a name="create-the-parallelcp-script"></a>Создание скрипта параллелкп

Приведенный ниже сценарий добавит исполняемый файл `parallelcp`. (Этот сценарий предназначен для Ubuntu. Если используется другой дистрибутив, необходимо установить ``parallel`` отдельно.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM 
#!/bin/bash

display_usage() { 
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n" 
} 

if [  \$# -le 1 ] ; then 
    display_usage
    exit 1
fi 
 
if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then 
    display_usage
    exit 0
fi 

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>Пример параллельного копирования

В этом примере используется скрипт параллельного копирования для компиляции ``glibc`` с использованием исходных файлов в кэше Azure HPC.

Исходные файлы кэшируются в точке подключения кэша HPC Azure, а объектные файлы хранятся на локальном жестком диске.

В этом примере для достижения параллелизации используется скрипт параллельного копирования с параметром ``-j`` и ``make``.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
