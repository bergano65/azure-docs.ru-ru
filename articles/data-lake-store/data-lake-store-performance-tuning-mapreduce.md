---
title: Настройка производительности Azure Data Lake Storage Gen1 - MapReduce
description: Рекомендации по настройке производительности для MapReduce в Azure Data Lake Storage 1-го поколения
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904594"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Рекомендации по настройке производительности для MapReduce в HDInsight и Azure Data Lake Storage 1-го поколения

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись Azure Data Lake Storage 1-го поколения**. Для получения инструкций о том, как его создать, [см.](data-lake-store-get-started-portal.md)
* **Кластер Azure HDInsight** с доступом к учетной записи Data Lake Storage 1-го поколения. Дополнительные сведения см. в статье [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Убедитесь, что вы включили удаленный рабочий стол для кластера.
* **Использование MapReduce на HDInsight**. См. дополнительные сведения об [использовании MapReduce в Hadoop и HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Просмотрите рекомендации по настройке производительности для Data Lake Storage Gen1**. Для общих концепций [Data Lake Storage Gen1 Performance Tuning Guidance](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) производительности см.

## <a name="parameters"></a>Параметры

Ниже перечислены наиболее важные параметры, которые можно настроить, чтобы оптимизировать производительность Data Lake Storage 1-го поколения при выполнении заданий MapReduce.

|Параметр      | Описание  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Количество памяти, чтобы выделить для каждого картографа.  |
|`Mapreduce.job.maps`     |  Количество задач карты на задание.  |
|`Mapreduce.reduce.memory.mb`     |  Количество памяти, чтобы выделить для каждого редуктора.  |
|`Mapreduce.job.reduces`    |   Количество задач сокращения на задания.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce.map.memory / Mapreduce.reduce.memory

Отрегулируйте это число в зависимости от того, сколько памяти необходимо для карты и/или уменьшите задачу. Вы можете просматривать значения `mapreduce.map.memory` по `mapreduce.reduce.memory` умолчанию и в Ambari через конфигурацию Yarn. В Амбари перейдите на YARN и просмотрите вкладку **Configs.** Память YARN будет отображаться.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce.job.maps / Mapreduce.job.reduces

Это определяет максимальное количество картографов или редукторов для создания. Количество сплитов определяет количество картографов, созданных для задания MapReduce. Таким образом, вы можете получить меньше картографов, чем вы просили, если Есть меньше расколов, чем количество картографов просил.

## <a name="guidance"></a>Руководство

### <a name="step-1-determine-number-of-jobs-running"></a>Шаг 1: Определить количество рабочих мест

По умолчанию MapReduce будет использовать весь кластер для обработки задания. Вы можете использовать меньше кластера, используя меньше картографов, чем есть доступные контейнеры. В этой статье предполагается, что ваше приложение — единственное, которое выполняется в кластере.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Шаг 2: Установите mapreduce.map.memory/mapreduce.reduce.memory

Объем памяти для задач сопоставления и уменьшения будет зависеть от конкретного задания. Можно уменьшить объем памяти, если нужно повысить параллелизм. Число одновременно выполняемых задач зависит от числа контейнеров. Сокращая объем памяти для каждого модуля сопоставления или уменьшения, можно создавать дополнительные контейнеры, обеспечивая одновременный запуск большего числа модулей сопоставления или уменьшения. Слишком сильное снижение объема памяти может вызвать нехватку памяти при выполнении некоторых процессов. Если вы получаете ошибку кучи при запуске вашей работы, увеличить память на картограф или редуктора. Учтите, что добавление дополнительных контейнеров добавляет дополнительные накладные расходы для каждого дополнительного контейнера, что потенциально может привести к ухудшить производительность. Альтернатива — обеспечить больший объем памяти, используя кластер с большим объемом памяти или увеличивая число узлов в кластере. Больший объем памяти позволит использовать дополнительные контейнеры, обеспечивая возможность параллельной обработки.

### <a name="step-3-determine-total-yarn-memory"></a>Шаг 3: Определить общую память YARN

Чтобы настроить mapreduce.job.maps/mapreduce.job.reduces, рассмотрим количество общей памяти YARN, доступной для использования. Эта информация доступна в Ambari. Перейдите к YARN и просмотрите вкладку **Configs.** Память YARN отображается в этом окне. Умножьте память YARN с числом узлов в кластере, чтобы получить общую память YARN.

`Total YARN memory = nodes * YARN memory per node`

Если вы используете пустое скопление, то память может быть общей памятью YARN для вашего кластера. Если другие приложения используют память, вы можете ограничить выделение памяти кластера, сократив число модулей сопоставления или уменьшения в соответствии с числом контейнеров, которые вы хотите использовать.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Шаг 4: Рассчитать количество контейнеров YARN

Контейнеры YARN определяют уровень параллелизма для задания. Разделите общий объем памяти YARN на значение mapreduce.map.memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Шаг 5. Установка mapreduce.job.maps/mapreduce.job.reduces

Установите для mapreduce.job.maps/mapreduce.job.reduces по крайней мере число доступных контейнеров. Вы можете поэкспериментировать, увеличивая число модулей сопоставления и уменьшения, чтобы понять, помогает ли это оптимизировать производительность. Имейте в виду, что большое число модулей сопоставления может увеличить нагрузку, что, в свою очередь, может привести к снижению производительности.

Возможности изоляции и планирования использования ресурсов ЦП отключены по умолчанию, следовательно, число контейнеров YARN ограничено объемом памяти.

## <a name="example-calculation"></a>Пример вычисления

Предположим, у вас есть кластер, который состоит из 8 узлов D14 и вы хотите запустить задание с большим количеством операций ввода-вывода. Ниже представлены примеры вычислений, которые нужно выполнить.

### <a name="step-1-determine-number-of-jobs-running"></a>Шаг 1: Определить количество рабочих мест

Например, мы предполагаем, что наша работа является единственной запущенной.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Шаг 2: Установите mapreduce.map.memory/mapreduce.reduce.memory

Например, вы проводите интенсивную работу по ввоцированию и решаете, что 3 ГБ памяти для задач карты достаточно.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Шаг 3: Определить общую память YARN

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Шаг 4. Расчет числа контейнеров YARN

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Шаг 5. Установка mapreduce.job.maps/mapreduce.job.reduces

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Ограничения

**Регулирование Data Lake Storage 1-го поколения**

Как мультитенантная служба, Data Lake Storage 1-го поколения определяет ограничения пропускной способности на уровне учетной записи. При превышении этих ограничений происходит сбой задач. Это можно определить, отслеживая ошибки регулирования в журналах задач. Если для обработки задания требуется большая пропускная способность, свяжитесь с нами.

Чтобы проверить, применяется ли для вас регулирование, включите ведение журнала отладки на стороне клиента. Вот как это сделать.

1. Включите следующее свойство в библиотеку Log4j в Ambari, последовательно выбрав YARN > Config > Advanced yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG.

2. Перезапустите все узлы и службы, чтобы изменения конфигурации вступили в силу.

3. Если вы получаете задушены, вы увидите код ошибки HTTP 429 в файле журнала YARN. Файл журнала YARN расположен здесь: /tmp/&lt;пользователь&gt;/yarn.log

## <a name="examples-to-run"></a>Примеры выполнения кода

Чтобы продемонстрировать, как MapReduce работает на Data Lake Storage Gen1, ниже приводится несколько примеров кода, который был запущен в кластере со следующими настройками:

* 16 узлов D14v2;
* кластер Hadoop под управлением HDI 3.6.

Для начала ознакомьтесь с примерами команд для запуска MapReduce Teragen, Terasort и Teravalidate. Эти команды можно настроить в соответствии с имеющимися ресурсами.

### <a name="teragen"></a>Тераген

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Терасорт

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Тералтер

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
