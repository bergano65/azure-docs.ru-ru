---
title: Отправка данных для заданий Apache Hadoop в HDInsight
description: Узнайте, как загрузить и получить доступ к данным для заданий Apache Hadoop в HDInsight. Используйте классический CLI Azure, Azure Storage Explorer, Azure PowerShell, командную строку Hadoop или Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdiseo17may2017
ms.date: 04/07/2020
ms.openlocfilehash: c862633245e75613f9e4f9956486f872b96239f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875016"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Отправка данных для заданий Apache Hadoop в HDInsight

HDInsight предоставляет распределенную файловую систему Hadoop (HDFS) через Хранилище Azure и хранилище озер данных Azure. Это хранилище включает Gen1 и Gen2. Лазурное хранилище и хранение данных озера Gen1 и Gen2 разработаны как расширения HDFS. Они позволяют полному набору компонентов в среде Hadoop работать непосредственно на данных, которыми она управляет. Azure Storage, Data Lake Storage Gen1 и Gen2 — это отдельные файловые системы. Системы оптимизированы для хранения данных и вычислений на этих данных. Для получения информации о преимуществах использования хранилища Azure [см.](hdinsight-hadoop-use-blob-storage.md) Смотрите также, [используйте данные озера хранения Gen1 с HDInsight](hdinsight-hadoop-use-data-lake-store.md), и [использовать данные озера хранения Gen2 с HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы необходимо ознакомиться со следующими требованиями:

* Кластер Azure HDInsight. Инструкции см. в статье [Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) или [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md).
* Изучите следующие статьи:
    * [Используйте хранилище Azure с помощью HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Использование Data Lake Store с кластерами Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Использование хранилища Azure Data Lake Storage Gen2 (предварительная версия) с кластерами Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Отправка данных в службу хранилища Azure

## <a name="utilities"></a>Техническое оборудование

Корпорация Майкрософт предоставляет следующие служебные программы для работы со службой хранилища Azure:

| Средство | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Портал Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [Azcopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Команда Hadoop](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Команда Hadoop доступна только в кластере HDInsight. Она допускает загрузку данных только из локальной файловой системы в службу хранилища Azure.  

## <a name="hadoop-command-line"></a>Командная строка Hadoop

Командная строка Hadoop полезна только для хранения данных в большом двоичном объекте службы хранилища Azure, когда данные уже присутствуют на головном узле кластера.

Чтобы использовать команду Hadoop, необходимо сначала подключиться к головному ноду с помощью [SSH или PuTTY.](hdinsight-hadoop-linux-use-ssh-unix.md)

После подключения можно использовать следующий синтаксис для отправки файла в хранилище.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Например: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Поскольку файловая система по умолчанию для HDInsight находится в Хранилище Azure, /пример/данные/data.txt на самом деле находится в Azure Storage. Можно также использовать следующую ссылку на файл:

    wasbs:///example/data/data.txt

or

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Список других команд Hadoop, которые работают с файлами, см.[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> В кластерах Apache HBase при записи данных используется размер блока по умолчанию (256 КБ). Это подходит, когда используются API HBase или REST API. Но при попытке записать более 12 ГБ данных с помощью команды `hadoop` или `hdfs dfs` возникнет ошибка. Подробные сведения см. в разделе [Исключение хранилища для записи большого двоичного объекта](#storage-exception-for-write-on-blob) этой статьи.

## <a name="graphical-clients"></a>Графические клиенты

Существуют также несколько приложений, которые предоставляют графический интерфейс для работы с хранилищем Azure. В следующей таблице приведен список некоторых из таких приложений:

| "Клиент"; | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools для HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Обозреватель службы хранилища Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer;](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer для Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Подключение службы хранилища Azure в качестве локального диска

Подробные сведения см. в статье [Mount Azure Blob Storage as Local Drive](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx) (Подключение хранилища BLOB-объектов Azure в качестве локального диска).

## <a name="upload-using-services"></a>Отправка с помощью служб

### <a name="azure-data-factory"></a>Фабрика данных Azure

Служба Azure Data Factory — это полностью управляемое обслуживание для составления данных: службы хранения, обработки и перемещения в рационализированные, адаптируемые и надежные конвейеры производства данных.

|Тип хранилища|Документация|
|----|----|
|Хранилище BLOB-объектов Azure|[Копирование данных в хранилище Azure Blob или из его хранилища с помощью Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Хранилище Azure Data Lake Storage 1-го поколения|[Копирование данных в Azure Data Lake Storage Gen1 и из него с помощью фабрики данных Azure](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage 2-го поколения |[Загрузка данных в Azure Data Lake Storage 2-го поколения с помощью Фабрики данных Azure](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop — это средство, предназначенное для передачи данных между Hadoop и реляционными базами данных. Используйте его для импорта данных из системы управления реляционными базами данных (RDBMS), такой как Сервер S'L, MyS'L или Oracle. Затем в распределенную файловую систему Hadoop (HDFS). Преобразуйте данные в Hadoop с помощью MapReduce или Hive, а затем экспортируете данные обратно в RDBMS.

Дополнительные сведения см. в разделе [Использование Sqoop с Hadoop в HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Пакеты SDK для разработки

Доступ к службе хранилища Azure также можно получить с помощью пакета Azure SDK со следующих языков программирования:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Подробные сведения об установке пакетов SDK для Azure см. на странице [Загрузки](https://azure.microsoft.com/downloads/).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="storage-exception-for-write-on-blob"></a>Исключение хранилища для записи в большой двоичный объект

**Симптомы**: `hadoop` При `hdfs dfs` использовании или команды, чтобы написать файлы, которые являются 12 ГБ или больше на кластере HBase, вы можете встретить следующую ошибку:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Причина**: HBase на кластерах HDInsight по умолчанию до размера блока 256 кБ при записи в хранилище Azure. Несмотря на то что это подходит при использовании API HBase или REST API, применение служебных программ командной строки `hadoop` или `hdfs dfs` вызовет ошибку.

**Решение.** Задайте больший размер блока с помощью свойства `fs.azure.write.request.size`. Эту модификацию можно сделать на основе `-D` использования, используя параметр. Далее приведен пример использования параметра с командой `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Можно также глобально увеличить значение `fs.azure.write.request.size` с помощью Apache Ambari. Чтобы изменить значение в веб-интерфейсе Ambari, сделайте следующее:

1. В браузере перейдите к веб-интерфейсу Ambari для кластера URL-адрес `https://CLUSTERNAME.azurehdinsight.net`— `CLUSTERNAME` это место, где находится название кластера. При появлении запроса введите имя и пароль администратора для кластера.
2. В левой части экрана выберите **HDFS**, а затем перейдите на вкладку **Configs** (Конфигурации).
3. В поле **Фильтр...** введите `fs.azure.write.request.size`.
4. Измените значение с 262 144 (256 КБ) на новое. Например, 4 194 304 (4 MB).

    ![Изображение смены значения через веб-интерфейс Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Подробные сведения об использовании Ambari см. в статье [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы понимаете, как получить данные в HDInsight, прочитайте следующие статьи, чтобы узнать анализ:

* [Начало работы с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Отправка заданий Apache Hadoop программными средствами](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hadoop/hdinsight-use-hive.md)
* [Использование Pig с Hadoop в HDInsight](./use-pig.md)
