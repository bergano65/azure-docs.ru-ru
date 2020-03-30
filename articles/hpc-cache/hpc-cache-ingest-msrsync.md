---
title: Лазурный HPC кэш данных ingest - msrsync
description: Как использовать msrsync для перемещения данных в цель хранения Blob в кэше HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168421"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Лазурный HPC кэш данных глотать - метод msrsync

В этой статье содержатся ``msrsync`` подробные инструкции по использованию утилиты для копирования данных в хранилище Azure Blob для использования в кэше Azure HPC.

Подробнее о перемещении данных в хранилище Blob для кэша Azure HPC читайте в [материале «Перемещение данных в хранилище Azure Blob».](hpc-cache-ingest.md)

Этот ``msrsync`` инструмент можно использовать для перемещения данных в цель хранения бэкэнда для кэша HPC Azure. Это средство предназначено для оптимизации использования пропускной способности путем запуска нескольких параллельных процессов ``rsync``. Оно доступно на сайте GitHub по адресу https://github.com/jbd/msrsync.

``msrsync`` разбивает исходный каталог на отдельные группы, а затем запускает отдельные процессы ``rsync`` в каждой группе.

Предварительное тестирование с использованием четырех основных виртуальных машин показало наилучшую эффективность при выполнении 64 процессов. Используйте параметр ``-p`` в ``msrsync``, чтобы настроить 64 процесса.

Обратите внимание, что ``msrsync`` может выполнять запись только в локальные тома и из них. Источник и пункт назначения должны быть доступны по мере того, как локальные крепления на рабочей станции используются для выдачи команды.

Следуйте этим инструкциям, которые следует использовать ``msrsync`` для заполнения хранилища Azure Blob с помощью кэша Azure HPC:

1. Установка ``msrsync`` и ее``rsync`` предпосылки (и Python 2.6 или более поздний)
1. Определите общее число копируемых файлов и каталогов.

   Например, используйте ``prime.py`` утилиту ```prime.py --directory /path/to/some/directory``` с аргументами <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>(доступно при загрузке).

   Если вы ``prime.py``не используете, вы можете рассчитать количество элементов с помощью инструмента GNU ``find`` следующим образом:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Разделите число элементов на 64, чтобы определить количество элементов для каждого процесса. Используйте это число с параметром ``-f``, чтобы задать размер групп при выполнении команды.

1. Выдать ``msrsync`` команду для копирования файлов:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Например, эта команда предназначена для перемещения 11 000 файлов в 64 процессах из /теста/источника-репозитория в /mnt/hpccache/репозиторий:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
