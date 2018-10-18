---
title: Копирование данных из WASB в Azure Data Lake Storage 1-го поколения и обратно с помощью DistCp | Документация Майкрософт
description: Узнайте, как с помощью средства DistCp копировать данные из Azure Storage Blobs (WASB) в Azure Data Lake Storage 1-го поколения и обратно.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 9740de34fe7cf7d06af1803cc6d77d7e89bbb73f
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391527"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Копирование данных между Azure Storage Blobs и Azure Data Lake Storage 1-го поколения
> [!div class="op_single_selector"]
> * [С помощью DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [С помощью AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

При наличии кластера HDInsight с доступом к Azure Data Lake Storage 1-го поколения с помощью таких средств экосистемы Hadoop, как DistCp, можно копировать данные **из системы хранения данных кластера HDInsight (WASB) в учетную запись Data Lake Storage 1-го поколения и обратно**. В этой статье содержатся инструкции по использованию средства DistCp.

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись Azure Data Lake Storage 1-го поколения**. За инструкциями по созданию учетной записи обращайтесь к статье [Начало работы с Azure Data Lake Storage 1-го поколения](data-lake-store-get-started-portal.md).
* **Кластер Azure HDInsight** с доступом к учетной записи Data Lake Storage 1-го поколения. Дополнительные сведения см. в статье [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Убедитесь, что вы включили удаленный рабочий стол для кластера.

## <a name="do-you-learn-fast-with-videos"></a>Учитесь быстрее с помощью видео?
[Просмотрите это видео](https://mix.office.com/watch/1liuojvdx6sie) о копировании данных между большими двоичными объектами службы хранилища Azure и Data Lake Storage 1-го поколения с помощью DistCp.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Использование Distcp из кластера HDInsight на платформе Linux

В состав кластера HDInsight входит служебная программа Distcp, которую можно использовать для копирования данных из различных источников в кластер HDInsight. При настройке кластера HDInsight для использования Data Lake Storage 1-го поколения в качестве дополнительного хранилища с помощью служебной программы DistCp можно копировать данные в учетную запись Data Lake Storage 1-го поколения и обратно без дополнительной настройки. В этом разделе мы рассмотрим, как использовать служебную программу DistCp.

1. На своем настольном компьютере используйте SSH, чтобы подключиться к кластеру. См. раздел [Подключение к кластеру HDInsight на основе Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Выполните команды в командной строке SSH.

2. Проверьте, доступны ли вам BLOB-объекты хранилища Azure (WASB). Выполните следующую команду:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Она должна вывести список содержимого в хранилище BLOB-объектов.

3. Аналогичным образом проверьте, доступна ли учетная запись хранилища Data Lake Storage 1-го поколения из кластера. Выполните следующую команду:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/

    Она должна вывести список файлов и папок в учетной записи хранилища Data Lake Storage 1-го поколения.

4. С помощью DistCp копируйте данные из WASB в учетную запись хранилища Data Lake Storage 1-го поколения.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder

    Эта команда копирует содержимое папки **/example/data/gutenberg/** в WASB в папку **/myfolder** в учетной записи Data Lake Storage 1-го поколения.

5. Аналогичным образом с помощью DistCp копируйте данные из учетной записи хранилища Data Lake Storage 1-го поколения в WASB.

        hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Эта команда копирует содержимое папки **/myfolder** в учетной записи Data Lake Storage 1-го поколения в папку **/example/data/gutenberg/** в WASB.

## <a name="performance-considerations-while-using-distcp"></a>Рекомендации по производительности при использовании DistCp

Так как наименьшей степенью детализации DistCp является один файл, установка максимального количества одновременных копий — это самый важный параметр оптимизации хранилища Data Lake Storage 1-го поколения. Количество одновременных копий регулируется установкой параметра, отвечающего за количество модулей сопоставления (m), в командной строке. Этот параметр указывает максимальное количество модулей сопоставления, которые используются для копирования данных. Значение по умолчанию — 20.

**Пример**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Как определить количество модулей сопоставления, которые следует использовать?

Ниже представлены некоторые полезные рекомендации.

* **Шаг 1. Определение общего объема памяти YARN.** Первым шагом является определение объема памяти YARN, доступной для кластера, в котором выполняется задание DistCp. Эта информация доступна на портале Ambari, связанном с кластером. Перейдите к YARN и откройте вкладку конфигураций, чтобы определить объем памяти YARN. Чтобы определить общий объем памяти YARN, умножьте объем памяти YARN для одного узла на количество узлов в кластере.

* **Шаг 2. Расчет количества модулей сопоставления.** Чтобы узнать значение **m**, разделите целую часть общего объема памяти YARN на размер контейнера YARN. Сведения о размере контейнера YARN также доступны на портале Ambari. Перейдите к YARN и откройте вкладку конфигураций. Размер контейнера YARN отобразится в этом окне. Уравнение для получения количества модулей сопоставления (**m**):

        m = (number of nodes * YARN memory for each node) / YARN container size

**Пример**

Предположим, что в кластере есть 4 узла D14v2s, и вы пытаетесь передать 10 ТБ данных из 10 разных папок. Эти папки содержат различные объемы данных, и размеры файлов в каждой папке отличаются.

* Общий объем памяти YARN. На портале Ambari вы узнали, что объем памяти YARN составляет 96 ГБ для узла D14. Таким образом, общий объем памяти YARN для кластера из четырех узлов равен: 

        YARN memory = 4 * 96GB = 384GB

* Число модулей сопоставления. На портале Ambari вы узнали, что размер контейнера YARN равен 3072 для узла кластера D14. Таким образом, число модулей сопоставления равно:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Если другие приложения используют память, то для DistCp можно использовать только часть памяти YARN кластера.

### <a name="copying-large-datasets"></a>Копирование больших наборов данных

Если размер перемещаемого набора данных большой (например, больше 1 ТБ), или в наборе много различных папок, рекомендуем использовать несколько заданий DistCp. Вряд ли это увеличит производительность, однако будет создано несколько заданий. В результате, если возникнет сбой одного из заданий, нужно будет перезапустить только это задание, а не все задание по перемещению.

### <a name="limitations"></a>Ограничения

* DistCp пытается создать модули сопоставления одинакового размера для оптимизации производительности. Увеличение количества модулей сопоставления не всегда приводит к увеличению производительности.

* DistCp ограничивается только одним модулем сопоставления для каждого файла. Поэтому количество модулей сопоставления не должно превышать количество файлов. Так как DistCp может назначать только один модуль сопоставления для одного файла, это ограничивает объем параллелизма, который можно использовать для копирования больших файлов.

* При наличии небольшого количества больших файлов следует разбить их на фрагменты по 256 МБ для повышения степени параллелизма. 
 
* При копировании из учетной записи хранилища BLOB-объектов Azure задание копирования может регулироваться на стороне хранилища BLOB-объектов. Это снижает производительность задания копирования. Дополнительные сведения об ограничениях хранилища BLOB-объектов Azure см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).

## <a name="see-also"></a>См. также
* [Копирование данных из больших двоичных объектов хранилища Azure в хранилище озера данных](data-lake-store-copy-data-azure-storage-blob.md)
* [Защита данных в Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Начало работы с Azure Data Lake Analytics с помощью портала Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
