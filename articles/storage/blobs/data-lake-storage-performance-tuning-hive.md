---
title: Рекомендации по настройке производительности Hive в Azure Data Lake Storage 2-го поколения | Документация Майкрософт
description: Рекомендации по настройке производительности Hive в Azure Data Lake Storage 2-го поколения
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b44c615396fbd526efb687977d303facd5d5d607
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939428"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Рекомендации по настройке производительности Hive в HDInsight и Azure Data Lake Storage 2-го поколения

По умолчанию устанавливаются параметры, которые должны обеспечить оптимальную производительность для самых разных сценариев использования.  Но для запросов с большим количеством операций ввода-вывода, можно повысить производительность Hive в Azure Data Lake Storage 2-го поколения, изменив некоторые настройки.  

## <a name="prerequisites"></a>Технические условия

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись Azure Data Lake Storage 2-го поколения**. См. инструкции по [созданию учетной записи хранения Azure Data Lake Storage 2-го поколения](data-lake-storage-quickstart-create-account.md).
* **Кластер Azure HDInsight** с доступом к учетной записи Data Lake Storage 2-го поколения. См. статью [Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).
* **Запустите Hive в HDInsight**.  Дополнительные сведения о выполнении заданий Hive в HDInsight см. в статье [Обзор Apache Hive и HiveQL в Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive).
* **Рекомендации по настройке производительности для Data Lake Storage 2-го поколения**.  Общие вопросы, связанные с производительностью, см. в [рекомендациях по настройке производительности Data Lake Storage 2-го поколения](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Параметры

Ниже приведены наиболее важные параметры, которые помогут повысить производительность Azure Data Lake Storage 2-го поколения:

* **hive.tez.container.size** — объем памяти, используемой для каждой задачи;

* **tez.grouping.min-size** — минимальный размер каждого модуля сопоставления;

* **tez.grouping.max-size** — максимальный размер каждого модуля сопоставления;

* **hive.exec.reducer.bytes.per.reducer** — размер каждого модуля уменьшения.

**hive.tez.container.size** — размер контейнера, который определяет объем доступной памяти для каждого задания.  Это главный параметр для управления параллелизмом в Hive.  

**tez.grouping.min-size** — этот параметр позволяет задать минимальный размер каждого модуля сопоставления.  Если число модулей сопоставления, которые выбирает Tez, окажется меньше, чем значение этого параметра, Tez будет использовать заданное здесь значение.

**tez.grouping.max-size** — этот параметр позволяет задать максимальный размер каждого модуля сопоставления.  Если число модулей сопоставления, которые выбирает Tez, окажется больше, чем значение этого параметра, Tez будет использовать заданное здесь значение.

**hive.exec.reducer.bytes.per.reducer** — этот параметр задает размер каждого модуля уменьшения.  По умолчанию используется размер 256 МБ.  

## <a name="guidance"></a>Руководство

**Задайте hive.exec.reducer.bytes.per.reducer** — значение по умолчанию хорошо работает для несжатых данных.  Если используются сжатые данные, попробуйте сократить размер модуля уменьшения редукции.  

**Задайте hive.tez.container.size** — объем памяти для каждого узла, который определяется параметром yarn.nodemanager.resource.memory-mb (должен быть правильно определен для кластера HDI по умолчанию).  Дополнительные сведения о настройке памяти в YARN см. в этой [записи блога](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Для интенсивных нагрузок ввода-вывода будет полезным увеличить параллелизм, снижая размер контейнера Tez. Это предоставит пользователю дополнительные контейнеры, то есть увеличит параллелизм.  Но некоторые запросы Hive требуют значительного объема памяти (например, MapJoin).  Если задача не получит достаточного объема памяти, во время выполнения возникнет соответствующее исключение.  Если вы заметите такие исключения, увеличьте объем памяти.   

Число одновременно выполняемых задач для параллелизма ограничивается общим объемом памяти YARN.  Число контейнеров YARN определяет, сколько параллельных задач можно запустить.  Чтобы узнать, сколько памяти YARN доступно для каждого узла, зайдите в Ambari.  Перейдите к YARN и откройте вкладку конфигураций.  Объем памяти YARN отобразится в этом окне.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
При использовании Azure Data Lake Storage 2-го поколения повышение производительности достигается за счет максимально возможного уровня параллелизма.  Tez автоматически вычисляет число задач, которые нужно создать, поэтому нет необходимости настраивать этот параметр.   

## <a name="example-calculation"></a>Пример вычисления

Предположим, что у вас есть кластер D14 с 8 узлами.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Дополнительные сведения о настройке Hive

Ниже приведены некоторые блоги, которые помогут в настройке производительности запросов Hive.
* [Оптимизация запросов Hive для Hadoop в HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Troubleshooting Hive query performance](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) (Устранение неполадок с производительностью запросов Hive)
* [Примите участие в дискуссии об оптимизации Hive на HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
