---
title: Запрос данных из HDFS-совместимой службы хранилища Azure в Azure HDInsight
description: Узнайте, как запрашивать данные из службы хранилища Azure и Azure Data Lake Storage для сохранения результатов анализа.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: cd6ba50cf81b93da887134e89d75313acb6bd936
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869854"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Использование службы хранилища Azure с кластерами Azure HDInsight

Вы можете хранить данные в [Хранилище Azure,](../storage/common/storage-introduction.md) [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)или [Azure Data Lake Storage Gen 2.](../storage/blobs/data-lake-storage-introduction.md) Или сочетание этих вариантов. Эти параметры хранения позволяют безопасно удалять кластеры HDInsight, которые используются для вычислений, не теряя при этом данные пользователей.

В Apache Hadoop поддерживается концепция файловой системы по умолчанию. Файловая система по умолчанию подразумевает использование центра сертификации и схемы по умолчанию. Она также может использоваться для разрешения относительных путей. В процессе создания кластера HDInsight можно указать контейнер с каплями в Хранилище Azure в качестве файловой системы по умолчанию. Или с ПОМОЩЬю HDInsight 3.6 вы можете выбрать систему хранения azure или Azure Data Lake Storage Gen 1/ Azure Data Lake Storage Gen 2 в качестве системы файлов по умолчанию за некоторыми исключениями. Дополнительные сведения о поддержке Data Lake Storage 1-го поколения в качестве связанного хранилища и хранилища по умолчанию см. в руководстве по [обеспечению доступности для кластеров HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Из этой статьи вы узнаете, как служба хранилища Azure работает с кластерами HDInsight. Чтобы узнать, как Data Lake Storage 1-го поколения работает с кластерами HDInsight, см. статью [Создание кластеров HDInsight, использующих Azure Data Lake Storage, с помощью портала Azure](hdinsight-hadoop-use-data-lake-store.md). Дополнительные сведения о создании кластера HDInsight см. в статье [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> Хранение учетной записи рода **BlobStorage** может быть использовано только в качестве вторичного хранения для кластеров HDInsight.

| Вид учетной записи хранилища | Поддерживаемые службы | Поддерживаемые уровни производительности |Не поддерживаемые уровни производительности| Поддерживаемые уровни доступа |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (учетная запись общего назначения версии 2)  | BLOB-объект     | Standard                    |Premium| Горячий, Прохладный, Архив\*   |
| Хранение (общее назначение v1)   | BLOB-объект     | Standard                    |Premium| Недоступно                    |
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

1. Создайте файл локального хранилища.

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

Для использования капли сначала создайте [учетную запись хранения Azure.](../storage/common/storage-create-storage-account.md) В рамках этого шага вы указываете область Azure, где создается учетная запись хранилища. Кластер и учетная запись хранения должны размещаться в одном регионе. База данных Hive metastore S'L Server и метамагазин Apache Oozie должны находиться в том же регионе.

Где бы ни находился созданный BLOB-объект, он принадлежит контейнеру в вашей учетной записи хранения Azure. Этот контейнер может быть существующим каплей, созданным за пределами HDInsight. Или это может быть контейнер, созданный для кластера HDInsight.

Стандартный контейнер больших двоичных объектов хранит сведения о кластере, включая журналы заданий. Не используйте стандартный контейнер BLOB-объектов с несколькими кластерами HDInsight. Это действие может привести к повреждению истории работы. Рекомендуется использовать другой контейнер для каждого кластера. Поместите общие данные на связанную учетную запись хранения, указанную для всех соответствующих кластеров, а не в учетную запись хранения по умолчанию. Дополнительные сведения о настройке связанных учетных записей хранения см. в статье [Создание кластеров HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Тем не менее, вы можете повторно использовать контейнер хранения по умолчанию после удаления исходного кластера HDInsight. Для кластеров HBase можно сохранить схему таблицы и данных HBase, создав новый кластер HBase с использованием контейнера по умолчанию, который используется удаленным кластером HBase

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Использование дополнительных учетных записей хранения

При создании кластера HDInsight укажите учетную запись хранения Azure, которую необходимо с ним связать. Кроме того, в процессе создания можно добавить дополнительные учетные записи хранения из той же подписки Azure или различных подписок Azure. Или после создания кластера. Инструкции по добавлению дополнительных учетных записей хранения см. в статье [Создание кластеров HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Использование дополнительной учетной записи хранения, расположение которой отличается от расположения кластера HDInsight, не поддерживается.

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как использовать HDFS-совместимую службу хранилища Azure с HDInsight. Это хранилище позволяет создавать адаптируемые, долгосрочные решения для архивирования данных и использовать HDInsight для разблокировки информации внутри сохраненных структурированных и неструктурированных данных.

Дополнительные сведения см. в разделе:

* [Начало работы с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Начало работы с Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Загрузка данных в HDInsight](hdinsight-upload-data.md)
* [Использование подписанных URL-адресов хранилища Azure для ограничения доступа к данным с помощью HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Используйте хранилище данных Azure Data Lake Gen2 с кластерами Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Руководство по извлечению, преобразованию и загрузке данных с помощью интерактивного запроса в Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
