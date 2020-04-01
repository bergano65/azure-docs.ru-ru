---
title: Запрос данных из HDFS-совместимой службы хранилища Azure в Azure HDInsight
description: Узнайте, как запрашивать данные из службы хранилища Azure и Azure Data Lake Storage для сохранения результатов анализа.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 6a4ae2932f8d294ecf71de0ae405204a1f4d7b4d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436942"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Использование службы хранилища Azure с кластерами Azure HDInsight

Для анализа данных в кластере HDInsight можно хранить данные либо в [Azure Storage,](../storage/common/storage-introduction.md) [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage Gen 2,](../storage/blobs/data-lake-storage-introduction.md)либо в комбинации. Эти параметры хранения позволяют безопасно удалять кластеры HDInsight, которые используются для вычислений, не теряя при этом данные пользователей.

В Apache Hadoop поддерживается концепция файловой системы по умолчанию. Файловая система по умолчанию подразумевает использование центра сертификации и схемы по умолчанию. Она также может использоваться для разрешения относительных путей. При создании кластера HDInsight в качестве файловой системы по умолчанию можно указать контейнер больших двоичных объектов в службе хранилища Azure, а в случае использования HDInsight 3.6 можно выбрать службу хранилища Azure или Azure Data Lake Storage (1-го или 2-го поколения) с несколькими исключениями. Дополнительные сведения о поддержке Data Lake Storage 1-го поколения в качестве связанного хранилища и хранилища по умолчанию см. в руководстве по [обеспечению доступности для кластеров HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Из этой статьи вы узнаете, как служба хранилища Azure работает с кластерами HDInsight. Чтобы узнать, как Data Lake Storage 1-го поколения работает с кластерами HDInsight, см. статью [Создание кластеров HDInsight, использующих Azure Data Lake Storage, с помощью портала Azure](hdinsight-hadoop-use-data-lake-store.md). Дополнительные сведения о создании кластера HDInsight см. в статье [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> Хранение учетной записи рода **BlobStorage** может быть использовано только в качестве вторичного хранения для кластеров HDInsight.

| Вид учетной записи хранилища | Поддерживаемые службы | Поддерживаемые уровни производительности |Не поддерживаемые уровни производительности| Поддерживаемые уровни доступа |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (учетная запись общего назначения версии 2)  | BLOB-объект     | Standard                    |Premium| Горячий, Прохладный, Архив\*   |
| Хранение (общее назначение v1)   | BLOB-объект     | Standard                    |Premium| Н/Д                    |
| BlobStorage                    | BLOB-объект     | Standard                    |Premium| Горячий, Прохладный, Архив\*   |

Применяемый по умолчанию контейнер больших двоичных объектов не рекомендуется использовать для хранения бизнес-данных. Чтобы сократить затраты на хранение, контейнер больших двоичных объектов по умолчанию рекомендуется удалять после каждого использования. Контейнер по умолчанию содержит журналы приложений и системный журнал. Обязательно извлеките эти журналы перед удалением контейнера.

Совместное использование одного контейнера с каплями в качестве файловой системы по умолчанию для нескольких кластеров не поддерживается.

> [!NOTE]  
> Уровень доступа Архива является автономным уровнем, который имеет несколько часов задержки поиска и не рекомендуется для использования с HDInsight. Дополнительные сведения см. в разделе [Архивный уровень доступа](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Доступ к файлам из кластера

Существует несколько способов доступа к файлам в Data Lake Storage из кластера HDInsight. Схема URI обеспечивает незашифрованный доступ (с *wasb:* префикс) и TLS зашифрованный доступ (с *wasbs*). Мы рекомендуем использовать *wasbs* всегда, когда это возможно, даже при обращении к данным, которые хранятся в том же регионе Azure.

* **С помощью полного доменного имени**. При таком подходе необходимо указать полный путь к файлу, к которому требуется доступ.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Используя сокращенный формат пути**. При таком подходе вы заменяете путь до корня кластера:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **С помощью относительного пути**. При таком подходе указывается только относительный путь к файлу, к которому требуется доступ.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Примеры доступа к данным

Примеры основаны на [съеме Ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) к головному узлам кластера. В примерах используются все три схемы URI. `CONTAINERNAME` Заменить `STORAGEACCOUNT` и с соответствующими значениями

#### <a name="a-few-hdfs-commands"></a>Несколько команд hdfs

1. Создайте простой файл локального хранилища.

    ```bash
    touch testFile.txt
    ```

1. Создание каталогов по кластерной памяти.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Копирование данных из локального хранилища в кластерное хранилище.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Список содержимого каталога на кластерных хранилищах.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> При работе с большими двоичными объектами вне HDInsight большинство программ не распознают формат WASB и вместо этого ожидают формат базового пути, например `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Создание таблицы Hive

Три местоположения файлов отображаются для иллюстративных целей. Для фактического выполнения используйте `LOCATION` только одну из записей.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>Доступ к файлам из внешнего кластера

Корпорация Майкрософт предоставляет следующие инструменты для работы с хранилищем Azure:

| Инструмент | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Портал Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [Azcopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Определить путь хранения из Амбари

* Чтобы определить полный путь к настроенной магазину по умолчанию, перейдите по:

    **HDFS** > **Configs** `fs.defaultFS` и введите в поле ввода фильтра.

* Чтобы проверить, настроен ли магазин wasb в качестве вторичного хранилища, перейдите по следующим данным:

    **HDFS** > **Configs** `blob.core.windows.net` и введите в поле ввода фильтра.

Чтобы получить путь с помощью Ambari REST API, [см.](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage)

## <a name="blob-containers"></a>Контейнеры больших двоичных объектов

Для использования капли сначала создайте [учетную запись хранения Azure.](../storage/common/storage-create-storage-account.md) В рамках этого процесса укажите регион Azure, в котором создается учетная запись хранения. Кластер и учетная запись хранения должны размещаться в одном регионе. База данных SQL Server хранилища метаданных Hive и база данных SQL Server хранилища метаданных Apache Oozie также должны располагаться в одном регионе.

Где бы ни находился созданный BLOB-объект, он принадлежит контейнеру в вашей учетной записи хранения Azure. Этим контейнером может быть существующий контейнер хранилища BLOB-объектов, созданный вне HDInsight, или контейнер, созданный для кластера HDInsight.

Стандартный контейнер больших двоичных объектов хранит сведения о кластере, включая журналы заданий. Не используйте стандартный контейнер BLOB-объектов с несколькими кластерами HDInsight. Это может привести к искажению истории заданий. Рекомендуется использовать другой контейнер для каждого кластера и поместить общие данные на связанную учетную запись хранения, указанную при развертывании всех соответствующих кластеров, а не на учетную запись хранения по умолчанию. Дополнительные сведения о настройке связанных учетных записей хранения см. в статье [Создание кластеров HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Тем не менее, вы можете повторно использовать контейнер хранения по умолчанию после удаления исходного кластера HDInsight. Для кластеров HBase можно сохранить схему таблицы и данные HBase, создав новый кластер HBase с помощью контейнера по умолчанию, используемого удаленным кластером HBase.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Использование дополнительных учетных записей хранения

При создании кластера HDInsight укажите учетную запись хранения Azure, которую необходимо с ним связать. Помимо этой учетной записи хранения в процессе создания или после создания кластера можно добавить дополнительные учетные записи хранения из той же или других подписок Azure. Инструкции по добавлению дополнительных учетных записей хранения см. в статье [Создание кластеров HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Использование дополнительной учетной записи хранения, расположение которой отличается от расположения кластера HDInsight, не поддерживается.

## <a name="next-steps"></a>Следующие шаги

Из этой статьи вы узнали, как использовать HDFS-совместимую службу хранилища Azure с HDInsight. Это позволяет создавать масштабируемые, долгосрочные решения для получения данных архивирования, а также использовать HDInsight для разблокирования информации внутри хранимых структурированных и неструктурированных данных.

Дополнительные сведения см. в разделе:

* [Начало работы с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Начало работы с Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Загрузка данных в HDInsight](hdinsight-upload-data.md)
* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hadoop/hdinsight-use-hive.md)
* [Использование подписанных URL-адресов хранилища Azure для ограничения доступа к данным с помощью HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Используйте хранилище данных Azure Data Lake Gen2 с кластерами Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Учебник: Извлекайте, преобразовывали и загружали данные с помощью интерактивного запроса в Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
