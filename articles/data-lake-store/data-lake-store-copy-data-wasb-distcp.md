---
title: Копирование данных в и из WASB в Azure Data Lake Storage Gen1 с помощью DistCp
description: Используйте инструмент DistCp для копирования данных в и из капли хранения Azure в Azure Data Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638839"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Используйте DistCp для копирования данных между каплями хранения Azure и Хранилищем озер данных Azure Gen1

> [!div class="op_single_selector"]
> * [С помощью DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [С помощью AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Если у вас есть кластер HDInsight с доступом к azure Data Lake Storage Gen1, вы можете использовать инструменты экосистемы Hadoop, такие как DistCp, для копирования данных и из кластерного хранилища HDInsight (WASB) в учетную запись Data Lake Storage Gen1. В этой статье показано, как использовать инструмент DistCp.

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись Azure Data Lake Storage 1-го поколения**. За инструкциями по созданию учетной записи обращайтесь к статье [Начало работы с Azure Data Lake Storage 1-го поколения](data-lake-store-get-started-portal.md).
* **Кластер Azure HDInsight** с доступом к учетной записи Data Lake Storage 1-го поколения. Дополнительные сведения см. в статье [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Убедитесь, что вы включили удаленный рабочий стол для кластера.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Использование Distcp из кластера HDInsight на платформе Linux

Кластер HDInsight поставляется с инструментом DistCp, который может быть использован для копирования данных из разных источников в кластер HDInsight. Если вы настроили кластер HDInsight для использования Data Lake Storage Gen1 в качестве дополнительного хранилища, вы можете использовать DistCp out-of-the box для копирования данных в учетную запись Data Lake Storage Gen1. В этом разделе мы рассмотрим, как использовать инструмент DistCp.

1. На своем настольном компьютере используйте SSH, чтобы подключиться к кластеру. См. раздел [Подключение к кластеру HDInsight на основе Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Выполните команды в командной строке SSH.

1. Установить, можно ли получить доступ к каплям хранения Azure (WASB). Выполните следующую команду:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   Выход предоставляет список содержимого в каплей для хранения.

1. Аналогичным образом проверьте, доступна ли учетная запись хранилища Data Lake Storage 1-го поколения из кластера. Выполните следующую команду:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    Выход предоставляет список файлов и папок в учетной записи Data Lake Storage Gen1.

1. Используйте DistCp для копирования данных с WASB на учетную запись Data Lake Storage Gen1.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    Эта команда копирует содержимое папки **/example/data/gutenberg/** в WASB в папку **/myfolder** в учетной записи Data Lake Storage 1-го поколения.

1. Аналогичным образом, используйте DistCp для копирования данных со счета data Lake Storage Gen1 в WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    Команда копирует содержимое **/myfolder** в учетной записи Data Lake Storage Gen1 в **/пример/данные/гутенберг/** папку в WASB.

## <a name="performance-considerations-while-using-distcp"></a>Рекомендации по производительности при использовании DistCp

Поскольку наименьшая детулярность инструмента DistCp является одним файлом, установление максимального количества одновременных копий является наиболее важным параметром для оптимизации его по отношению к Data Lake Storage Gen1. Вы можете управлять числом одновременных копий, установив параметр ('m') на командной строке. Этот параметр указывает максимальное количество модулей сопоставления, которые используются для копирования данных. Значение по умолчанию — 20.

Пример

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Как определить количество картографов для использования

Ниже представлены некоторые полезные рекомендации.

* **Шаг 1. Определение общего объема памяти YARN.** Первым шагом является определение объема памяти YARN, доступной для кластера, в котором выполняется задание DistCp. Эта информация доступна на портале Ambari, связанном с кластером. Перейдите к YARN и просмотрите вкладку **Configs,** чтобы увидеть память YARN. Чтобы определить общий объем памяти YARN, умножьте объем памяти YARN для одного узла на количество узлов в кластере.

* **Шаг 2. Расчет количества модулей сопоставления.** Чтобы узнать значение **m**, разделите целую часть общего объема памяти YARN на размер контейнера YARN. Информация о размерах контейнеров YARN также доступна на портале Ambari. Перейдите к YARN и просмотрите вкладку **Configs.** Размер контейнера YARN отображается в этом окне. Уравнение, чтобы достичь числа картографов **(м)** является:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Пример

Предположим, что в кластере есть четыре узла D14v2s, и вы хотите передать 10 ТБ данных из 10 различных папок. Эти папки содержат различные объемы данных, и размеры файлов в каждой папке отличаются.

* Общий объем памяти YARN. На портале Ambari вы узнали, что объем памяти YARN составляет 96 ГБ для узла D14. Таким образом, общий объем памяти YARN для кластера из четырех узлов равен:

   `YARN memory = 4 * 96GB = 384GB`

* Число модулей сопоставления. На портале Ambari вы узнали, что размер контейнера YARN равен 3072 для узла кластера D14. Таким образом, количество картографов:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Если другие приложения используют память, вы можете использовать только часть памяти YARN кластера для DistCp.

### <a name="copying-large-datasets"></a>Копирование больших наборов данных

Когда размер движимого набора данных велик (например, > 1 ТБ) или если у вас много разных папок, подумайте об использовании нескольких заданий DistCp. Там, вероятно, нет повышения производительности, но он распределяет задания так, что если какая-либо работа не удается, вам нужно только перезапустить эту конкретную работу, а не всю работу.

### <a name="limitations"></a>Ограничения

* DistCp пытается создать модули сопоставления одинакового размера для оптимизации производительности. Увеличение количества модулей сопоставления не всегда приводит к увеличению производительности.

* DistCp ограничивается только одним модулем сопоставления для каждого файла. Таким образом, вы не должны иметь больше картографов, чем у вас есть файлы. Поскольку DistCp может назначить файлу только один картограф, это ограничивает количество параллелизма, которое может быть использовано для копирования больших файлов.

* Если у вас есть небольшое количество больших файлов, разделите их на 256-МБ файл куски, чтобы дать вам больше потенциальных параллелизма.

* Если вы копируете с учетной записи хранения Azure Blob, ваша работа копий может быть задушена на стороне хранения Blob. Это снижает производительность задания копирования. Чтобы узнать больше о границах хранилища Azure Blob, [Azure subscription and service limits](../azure-resource-manager/management/azure-subscription-service-limits.md)см.

## <a name="see-also"></a>См. также

* [Копирование данных из капли хранения данных в Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Защита данных в Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Начало работы с Azure Data Lake Analytics с помощью портала Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
