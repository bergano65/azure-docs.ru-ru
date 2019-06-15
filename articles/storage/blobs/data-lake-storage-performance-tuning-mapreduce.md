---
title: Рекомендации по настройке производительности для MapReduce в Azure Data Lake Storage 2-го поколения | Документы Майкрософт
description: Рекомендации по настройке производительности для MapReduce в Azure Data Lake Storage 2-го поколения
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 7d20f1b398c50a3b98ee862332338dbf3aaece59
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939385"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Рекомендации по настройке производительности для MapReduce в HDInsight и Azure Data Lake Storage 2-го поколения

Изучите факторы, которые важны для настройки производительности заданий Map Reduce. В этой статье рассматривается ряд рекомендаций по улучшению производительности.

## <a name="prerequisites"></a>Технические условия

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись Azure Data Lake Storage 2-го поколения**. См. инструкции по [созданию. Создание поддерживаемой учетной записи хранения Azure Data Lake Storage 2-го поколения](data-lake-storage-quickstart-create-account.md).
* **Кластер Azure HDInsight** с доступом к учетной записи Data Lake Storage 2-го поколения. См. статью [Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).
* **Использование MapReduce в HDInsight**.  См. дополнительные сведения об [использовании MapReduce в Hadoop и HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Рекомендации по настройке производительности для Data Lake Storage 2-го поколения**.  Общие вопросы, связанные с производительностью, см. в [рекомендациях по настройке производительности Data Lake Storage 2-го поколения](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Параметры

Ниже перечислены параметры, которые можно настроить, чтобы оптимизировать производительность Data Lake Storage 2-го поколения при выполнении заданий MapReduce.

* **Mapreduce.map.memory.mb** — объем памяти, выделяемой для каждого модуля сопоставления.
* **Mapreduce.job.maps** — число задач сопоставления на задание.
* **Mapreduce.reduce.memory.mb** — объем памяти, выделяемой для каждого модуля уменьшения.
* **Mapreduce.job.reduces** — число задач уменьшения на задание.

**Mapreduce.map.memory или Mapreduce.reduce.memory** — это число настраивается в зависимости от объема памяти, требуемой для задачи сопоставления или уменьшения.  Стандартные значения mapreduce.map.memory и mapreduce.reduce.memory можно просмотреть в Ambari в конфигурации YARN.  В Ambari перейдите к YARN и откройте вкладку конфигураций.  Отобразится объем памяти YARN.  

**Mapreduce.job.maps или Mapreduce.job.reduces** — определяет максимальное число создаваемых модулей сопоставления или уменьшения.  Количество разделений определяет число модулей сопоставления, создаваемых для задания MapReduce.  Следовательно, если разделений меньше, чем запрошенных модулей сопоставления, вы можете получить меньше модулей сопоставления, чем планировалось.       

## <a name="guidance"></a>Руководство

> [!NOTE]
> В этой статье предполагается, что ваше приложение — единственное, которое выполняется в кластере.

**Шаг 1. Определение числа запущенных заданий**

По умолчанию MapReduce будет использовать весь кластер для обработки задания.  Можно выделить меньший объем кластера, используя меньшее модулей сопоставления, чем доступных контейнеров.        

**Шаг 2. Настройка mapreduce.map.memory/mapreduce.reduce.memory**

Объем памяти для задач сопоставления и уменьшения будет зависеть от конкретного задания.  Можно уменьшить объем памяти, если нужно повысить параллелизм.  Число одновременно выполняемых задач зависит от числа контейнеров.  Сокращая объем памяти для каждого модуля сопоставления или уменьшения, можно создавать дополнительные контейнеры, обеспечивая одновременный запуск большего числа модулей сопоставления или уменьшения.  Слишком сильное снижение объема памяти может вызвать нехватку памяти при выполнении некоторых процессов.  Если при выполнении задания возникает ошибка кучи, следует увеличить объем памяти для модуля сопоставления или уменьшения.  При этом следует учитывать следующее. Добавление нескольких контейнеров ведет к увеличению нагрузки для каждого дополнительного контейнера, из-за которой может снизиться производительность.  Альтернатива — обеспечить больший объем памяти, используя кластер с большим объемом памяти или увеличивая число узлов в кластере.  Больший объем памяти позволит использовать дополнительные контейнеры, обеспечивая возможность параллельной обработки.  

**Шаг 3. Определение общей памяти YARN**

Для настройки mapreduce.job.maps/mapreduce.job.reduces следует учитывать общий объем памяти YARN, доступный для использования.  Эта информация доступна в Ambari.  Перейдите к YARN и откройте вкладку конфигураций.  Объем памяти YARN отобразится в этом окне.  Чтобы определить общий объем памяти YARN, умножьте объем памяти YARN для одного узла на число узлов в кластере.

    Total YARN memory = nodes * YARN memory per node

Если используется пустой кластер, это значение может быть представлено общим объемом памяти YARN для кластера.  Если другие приложения используют память, вы можете ограничить выделение памяти кластера, сократив число модулей сопоставления или уменьшения в соответствии с числом контейнеров, которые вы хотите использовать.  

**Шаг 4. Расчет числа контейнеров YARN**

Контейнеры YARN определяют уровень параллелизма для задания.  Разделите общий объем памяти YARN на значение mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Шаг 5. Установка mapreduce.job.maps/mapreduce.job.reduces**

Установите для mapreduce.job.maps/mapreduce.job.reduces по крайней мере число доступных контейнеров.  Вы можете поэкспериментировать, увеличивая число модулей сопоставления и уменьшения, чтобы понять, помогает ли это оптимизировать производительность.  Имейте в виду, что большое число модулей сопоставления может увеличить нагрузку, что, в свою очередь, может привести к снижению производительности.  

Возможности изоляции и планирования использования ресурсов ЦП отключены по умолчанию, следовательно, число контейнеров YARN ограничено объемом памяти.

## <a name="example-calculation"></a>Пример вычисления

Предположим, у вас есть кластер, который состоит из 8 узлов D14 и вы хотите запустить задание с большим количеством операций ввода-вывода.  Ниже представлены примеры вычислений, которые нужно выполнить.

**Шаг 1. Определение числа запущенных заданий**

В этом примере давайте предположим, что наше задание является единственным, которое выполняется.  

**Шаг 2. Настройка mapreduce.map.memory/mapreduce.reduce.memory**

В этом примере мы выполняем интенсивное задание ввода-вывода и решаем, что для задач сопоставления будет достаточно 3 ГБ памяти.

    mapreduce.map.memory = 3GB

**Шаг 3. Определение общей памяти YARN**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Шаг 4. Расчет числа контейнеров YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Шаг 5. Установка mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Примеры выполнения кода

Чтобы показать, как MapReduce выполняется в Data Lake Storage 2-го поколения, ниже приведен пример кода, выполняемого в кластере со следующими параметрами:

* 16 узлов D14v2;
* кластер Hadoop под управлением HDI 3.6.

Для начала ознакомьтесь с примерами команд для запуска MapReduce Teragen, Terasort и Teravalidate.  Эти команды можно настроить в соответствии с имеющимися ресурсами.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
