---
title: Отправка данных для заданий Apache Hadoop в HDInsight
description: Сведения об отправке данных и получении доступа к ним для заданий Apache Hadoop в HDInsight с помощью классического интерфейса командной строки Azure, Обозревателя службы хранилища Azure, Azure PowerShell, командной строки Hadoop или Sqoop.
keywords: etl hadoop, вставка данных в hadoop, загрузка данных hadoop
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/08/2019
ms.openlocfilehash: a56f2546eec12d04dcc64665e60a9874b8e991b6
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684146"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Отправка данных для заданий Apache Hadoop в HDInsight

Служба Azure HDInsight — это полнофункциональная распределенная файловая система Hadoop (HDFS), в основе которой лежит служба хранилища Azure и Azure Data Lake Storage (1-го и 2-го поколения). Служба хранилища Azure и Data Lake Storage 1-го и 2-го поколения, разработанные в качестве расширений HDFS, обеспечивают клиентам высочайшее удобство работы. Благодаря им все компоненты экосистемы Hadoop работают непосредственно с данными, управляемыми службой. Служба хранилища Azure, а также Data Lake Storage 1-го и 2-го поколения — это разные файловые системы, оптимизированные для хранения и обработки данных. Сведения о преимуществах использования службы хранилища Azure см. в статьях [Использование службы хранилища Azure с кластерами Azure HDInsight][hdinsight-storage], [Использование Data Lake Storage 1-го поколения с кластерами Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md) и [Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Технические условия

Перед началом работы необходимо ознакомиться со следующими требованиями:

* Кластер Azure HDInsight. Инструкции см. в статье [Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight][hdinsight-get-started] или [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md).
* Изучите следующие статьи:

    - [Использование службы хранилища Azure с HDInsight][hdinsight-storage]
    - [Использование Data Lake Store с кластерами Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    - [Использование хранилища Azure Data Lake Storage Gen2 (предварительная версия) с кластерами Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Отправка данных в службу хранилища Azure

## <a name="utilities"></a>Техническое оборудование
Корпорация Майкрософт предоставляет следующие служебные программы для работы со службой хранилища Azure:

| Средство | Linux | OS X |  Windows |
| --- |:---:|:---:|:---:|
| [портал Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Интерфейс командной строки Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Команда Hadoop](#commandline) |✔ |✔ |✔ |


> [!NOTE]  
> Команда Hadoop доступна только в кластере HDInsight. Она допускает загрузку данных только из локальной файловой системы в службу хранилища Azure.  


## <a id="commandline"></a>Командная строка Hadoop
Командная строка Hadoop полезна только для хранения данных в большом двоичном объекте службы хранилища Azure, когда данные уже присутствуют на головном узле кластера.

Чтобы использовать команду Hadoop, необходимо сначала подключиться к головному узлу с помощью [SSH или PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

После подключения можно использовать следующий синтаксис для отправки файла в хранилище.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Например `hadoop fs -copyFromLocal data.txt /example/data/data.txt`.

Так как файловая система по умолчанию для HDInsight находится в службе хранилища Azure, файл /example/data.txt фактически располагается там же. Можно также использовать следующую ссылку на файл:

    wasb:///example/data/data.txt

или

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Список других команд Hadoop, которые работают с файлами, см. здесь: [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> В кластерах Apache HBase при записи данных используется размер блока по умолчанию (256 КБ). Это подходит, когда используются API HBase или REST API. Но при попытке записать более 12 ГБ данных с помощью команды `hadoop` или `hdfs dfs` возникнет ошибка. Подробные сведения см. в разделе [Исключение хранилища для записи большого двоичного объекта](#storageexception) этой статьи.

## <a name="graphical-clients"></a>Графические клиенты
Существуют также несколько приложений, которые предоставляют графический интерфейс для работы с хранилищем Azure. В следующей таблице приведен список некоторых из таких приложений:

| Клиент | Linux | OS X |  Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools для HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer;](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer;](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer для Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |


## <a name="mount-azure-storage-as-local-drive"></a>Подключение службы хранилища Azure в качестве локального диска
Подробные сведения см. в статье [Mount Azure Blob Storage as Local Drive](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx) (Подключение хранилища BLOB-объектов Azure в качестве локального диска).

## <a name="upload-using-services"></a>Отправка с помощью служб
### <a name="azure-data-factory"></a>Фабрика данных Azure
Фабрика данных Azure является полностью управляемой системой для создания служб хранения, обработки и перемещения данных и превращения этих служб в упрощенные, масштабируемые и надежные конвейеры производства данных.

|Тип хранилища|Документация|
|----|----|
|Хранилище больших двоичных объектов Azure|[Копирование данных в хранилище BLOB-объектов Azure и обратно с помощью фабрики данных Azure](../data-factory/connector-azure-blob-storage.md)|
|Хранилище Azure Data Lake Gen1|[Копирование данных в Azure Data Lake Storage 1-го поколения и из него с помощью фабрики данных Azure](../data-factory/connector-azure-data-lake-store.md)|
|Хранилище Azure Data Lake Gen2 |[Загрузка данных в Azure Data Lake Storage 2-го поколения с помощью Фабрики данных Azure](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop
Sqoop — это средство, предназначенное для передачи данных между Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционными базами данных (РСУБД), например SQL Server, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в системе Hadoop с использованием MapReduce или Hive, а затем экспортировать данные обратно в РСУБД.

Дополнительные сведения см. в разделе [Использование Sqoop с Hadoop в HDInsight][hdinsight-use-sqoop].

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
### <a id="storageexception"></a>Исключение хранилища для записи в большой двоичный объект
**Проблема.** Если при использовании команд `hadoop` или `hdfs dfs` в кластер HBase записываются файлы размером более 12 ГБ, может возникнуть следующая ошибка:

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

**Причина.** В HBase в кластерах HDInsight размер блока по умолчанию при записи в службу хранилища Azure равен 256 КБ. Несмотря на то что это подходит при использовании API HBase или REST API, применение служебных программ командной строки `hadoop` или `hdfs dfs` вызовет ошибку.

**Решение**. Задайте больший размер блока с помощью свойства `fs.azure.write.request.size`. Его можно указывать для каждого отдельного случая, используя параметр `-D`. Далее приведен пример использования параметра с командой `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Можно также глобально увеличить значение `fs.azure.write.request.size` с помощью Apache Ambari. Чтобы изменить значение в веб-интерфейсе Ambari, сделайте следующее:

1. В браузере перейдите к веб-интерфейсу Ambari для кластера Это https://CLUSTERNAME.azurehdinsight.net, где **ИМЯ_КЛАСТЕРА** — имя кластера.

    При появлении запроса введите имя и пароль администратора для кластера.
2. В левой части экрана выберите **HDFS**, а затем перейдите на вкладку **Configs** (Конфигурации).
3. В поле **Фильтр...** введите `fs.azure.write.request.size`. Посредине страницы отобразится поле и его текущее значение.
4. Измените значение с 262 144 (256 КБ) на новое. Например, 4 194 304 (4 MB).

![Изображение смены значения через веб-интерфейс Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Подробные сведения об использовании Ambari см. в статье [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы знаете, как передавать данные в HDInsight, узнайте, как их можно анализировать.

* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][hdinsight-get-started]
* [Отправка заданий Apache Hadoop программными средствами][hdinsight-submit-jobs]
* [Использование Apache Hive с HDInsight][hdinsight-use-hive]
* [Использование Apache Pig с HDInsight][hdinsight-use-pig]

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
