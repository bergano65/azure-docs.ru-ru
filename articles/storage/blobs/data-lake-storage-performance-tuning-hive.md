---
title: 'Настройка производительности: Hive, HDInsight & Azure Data Lake Storage 2-го поколения | Документация Майкрософт'
description: Общие сведения о рекомендациях по настройке запросов ввода-вывода с использованием Hive, HDInsight и Azure Data Lake Storage 2-го поколения.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: fb908fe94f940073753ea8e1cde3da2b2a0c4b6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88034776"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Настройка производительности: Hive, HDInsight & Azure Data Lake Storage 2-го поколения

По умолчанию устанавливаются параметры, которые должны обеспечить оптимальную производительность для самых разных сценариев использования.  Но для запросов с большим количеством операций ввода-вывода, можно повысить производительность Hive в Azure Data Lake Storage 2-го поколения, изменив некоторые настройки.  

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись Azure Data Lake Storage 2-го поколения**. Инструкции по ее созданию см. в разделе [Краткое руководство. Создание учетной записи хранения Azure Data Lake Storage 2-го поколения](data-lake-storage-quickstart-create-account.md)
* **Кластер Azure HDInsight** с доступом к учетной записи Data Lake Storage 2-го поколения. См. статью [использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) .
* **Запустите Hive в HDInsight**.  Дополнительные сведения о выполнении заданий Hive в HDInsight см. в статье [Обзор Apache Hive и HiveQL в Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive).
* **Рекомендации по настройке производительности для Data Lake Storage 2-го поколения**.  Общие вопросы, связанные с производительностью, см. в [рекомендациях по настройке производительности Data Lake Storage 2-го поколения](data-lake-storage-performance-tuning-guidance.md)

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

Число одновременно выполняемых задач для параллелизма ограничивается общим объемом памяти YARN.  Число контейнеров YARN определяет, сколько параллельных задач можно запустить.  Чтобы узнать, сколько памяти YARN доступно для каждого узла, зайдите в Ambari.  Перейдите по адресу YARN и просмотрите вкладку configs (конфигурации).  В этом окне отображается память YARN.  

- Общий объем памяти YARN = узлы * YARN память на узел
- \# YARN Containers = Total YARN память/Tez размер контейнера

При использовании Azure Data Lake Storage 2-го поколения повышение производительности достигается за счет максимально возможного уровня параллелизма.  Tez автоматически вычисляет число задач, которые нужно создать, поэтому нет необходимости настраивать этот параметр.   

## <a name="example-calculation"></a>Пример вычисления

Предположим, что у вас есть кластер D14 с 8 узлами.  

- Общий объем памяти YARN = узлы * YARN память на узел
- Общий объем памяти YARN = 8 узлов * 96 ГБ = 768GB
- \# контейнеров YARN = 768GB/3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Дополнительные сведения о настройке Hive

Ниже приведены некоторые блоги, которые помогут в настройке производительности запросов Hive.
* [Оптимизация запросов Hive для Hadoop в HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Оптимизация запросов Apache в Hive в Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query)
* [Примите участие в дискуссии об оптимизации Hive на HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
