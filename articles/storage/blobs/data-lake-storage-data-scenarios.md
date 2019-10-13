---
title: Сценарии работы с данными с использованием Azure Data Lake Storage 2-ого поколения | Документация Майкрософт
description: Сведения о разных сценариях и средствах для приема, обработки, скачивания и визуализации данных в Data Lake Storage 2-ого поколения (прежнее название — Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: eba0c6a8932a8c6d50bd98d94712c95516519274
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300329"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Использование Azure Data Lake Storage 2-ого поколения для обеспечения соответствия требованиям больших данных

Процесс обработки больших данных состоит из указанных далее четырех ключевых этапов.

> [!div class="checklist"]
> * Прием больших объемов данных в хранилище данных в режиме реального времени или в пакетах
> * Обработка данных
> * Загрузка данных
> * Визуализация данных

Начните с создания учетной записи хранения и контейнера. Затем предоставьте доступ к данным. Начальные разделы этой статьи помогут вам выполнить эти задачи. В остальных разделах мы рассмотрим параметры и инструменты для каждого этапа обработки.

Полный список служб Azure, которые можно использовать с Azure Data Lake Storage 2-го поколения, см. в статье [интеграция Azure Data Lake Storage со службами Azure](data-lake-store-integrate-with-azure-services.md) .

## <a name="create-a-data-lake-storage-gen2-account"></a>Создание учетной записи Azure Data Lake Storage 2-го поколения

Учетная запись Azure Data Lake Storage 2-го поколения — это учетная запись хранения, использующая иерархическое пространство имен. 

Чтобы создать ее, ознакомьтесь с разделом [Краткое руководство. Создание поддерживаемой учетной записи хранения Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="create-a-container"></a>Создание контейнера

Ниже приведен список средств, которые можно использовать для создания контейнера для файлов.

|Средство | Руководство |
|---|--|
|Обозреватель службы хранилища Azure | [Создание контейнера с помощью Обозреватель службы хранилища](data-lake-storage-explorer.md#create-a-container) |
|AzCopy | [Создание контейнера больших двоичных объектов или общего файлового ресурса с помощью AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|Интерфейс командной строки (CLI) контейнера Hadoop с HDInsight |[Создание контейнера с помощью HDFS с HDInsight](data-lake-storage-use-hdfs-data-lake-storage.md#create-a-container) |
|Код в записной книжке Azure Databricks|[Создание контейнера учетной записи хранения (Scala)](data-lake-storage-quickstart-create-databricks-account.md#create-storage-account-container) <br><br> [Создание контейнера и его подключение (Python)](data-lake-storage-use-databricks-spark.md#create-a-container-and-mount-it)|

Проще всего создавать файловые системы с помощью Обозревателя службы хранилища или AzCopy. Создавать файловые системы с помощью HDInsight и Databricks несколько сложнее. Тем не менее, если вы все равно планируете использовать кластеры HDInsight или Databricks для обработки данных, то можете сначала создать кластеры, а затем создать файловые системы с помощью интерфейса командной строки HDFS.  

## <a name="grant-access-to-the-data"></a>Предоставление доступа к данным

Настройте соответствующие разрешения на доступ к своей учетной записи и данным в ней, прежде чем начать прием данных.

Предоставить доступ можно тремя способами.

* Назначьте одну из этих ролей пользователю, группе, управляемому пользователем удостоверению или субъекту-службе:

  [читатель данных больших двоичных объектов хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader).

  [участник данных BLOB-объектов хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor);

  [владелец данных BLOB-объектов хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner);

* Используйте маркер подписанного URL-адреса (SAS).

* Используйте ключ учетной записи хранения.

В этой таблице показано, как для предоставить доступ различным службам Azure и инструментам.

|Средство | Предоставление доступа | Руководство |
|---|--|---|
|Обозреватель службы хранилища| Назначение роли пользователям и группам. | [Назначение ролей администратора и других ролей пользователям с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Назначение роли пользователям и группам. <br>**or**<br> Использование маркера SAS.| [Назначение ролей администратора и других ролей пользователям с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Простое создание подписанного URL-адреса для скачивания файла из службы хранилища Azure с помощью Обозревателя службы хранилища Azure](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Назначение роли для управляемого удостоверения, назначаемого пользователем | [Создание кластера HDInsight с Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Фабрика данных Azure| Назначение роли для управляемого удостоверения, назначаемого пользователем<br>**or**<br> Назначение роли субъекту-службе.<br>**or**<br> использование ключа учетной записи хранения. | [Свойства связанной службы](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Назначение роли для управляемого удостоверения, назначаемого пользователем | [Создание кластера HDInsight с Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Назначение роли субъекту-службе. | [Практическое руководство. Создание приложения Azure Active Directory и субъект-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Чтобы предоставить доступ к определенным файлам и папкам, ознакомьтесь с приведенными ниже статьями.

* [Установка разрешений на уровне файлов и каталогов в Azure Data Lake Storage 2-го поколения с помощью Обозревателя службы хранилища Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Списки управления доступом для файлов и каталогов](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Дополнительные сведения о настройке других аспектов безопасности см. в [руководстве по безопасности для Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Прием данных

В этом разделе описываются различные источники данных и способы поступления этих данных в учетную запись Azure Data Lake Storage 2-го поколения.

![Прием данных в Data Lake Storage 2-го поколения](./media/data-lake-storage-data-scenarios/ingest-data.png "Прием данных в Data Lake Storage 2-го поколения")

### <a name="ad-hoc-data"></a>Специальные данные

Это небольшие наборы данных, которые используются для создания прототипов приложений для работы с большими данными. В зависимости от источника данных применяются разные способы приема специальных данных. 

Ниже приведен список инструментов, которые можно использовать для приема специальных данных.

| Источник данных | Средство для приема |
| --- | --- |
| Локальный компьютер |[Обозреватель хранилища](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Средство AzCopy ](../common/storage-use-azcopy-v10.md)|
| Большой двоичный объект хранилища Azure |[Фабрика данных Azure](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Средство AzCopy ](../common/storage-use-azcopy-v10.md)<br><br>[DistCp, запущенный на кластере HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Потоковые данные

Это данные, создаваемые различными источниками, такими как приложения, устройства, датчики и т. д. Для ввода этих данных в Data Lake Storage 2-го поколения можно использовать разные средства. Как правило, эти средства собирают и обрабатывают данные на основе событий в режиме реального времени, а затем записывают события в пакетном режиме в Data Lake Storage 2-го поколения для последующей обработки.

Ниже приведен список инструментов, которые можно использовать для приема потоковых данных.

|Средство | Руководство |
|---|--|
|Azure HDInsight Storm | [Запись данных в Apache Hadoop HDFS из Apache Storm в HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Реляционные данные

Можно также извлекать данные из реляционных баз данных. В течение определенного периода времени реляционные базы данных собирают огромные объемы данных, которые после обработки с помощью конвейера больших данных могут предоставлять ценные сведения. Для перемещения таких данных в Data Lake Storage 2-го поколения можно использовать следующие средства.

Ниже приведен список инструментов, которые можно использовать для приема реляционных данных.

|Средство | Руководство |
|---|--|
|Фабрика данных Azure | [Действие копирования в Фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Данные журналов веб-сервера (отправка с помощью настраиваемых приложений)

Этот тип набора данных вызывается специально, так как анализ данных журналов веб-сервера часто используется в приложениях по работе с большими данными, и для его выполнения требуется отправка больших объемов файлов журналов в Data Lake Storage 2-го поколения. Для отправки таких данных воспользуйтесь следующими средствами или напишите собственные сценарии или приложения.

Ниже приведен список инструментов, которые можно использовать для приема данных журнала веб-сервера.

|Средство | Руководство |
|---|--|
|Фабрика данных Azure | [Действие копирования в Фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Отличным способом отправки данных журналов веб-сервера и других типов данных (например данных общественных мнений) является использование собственных написанных сценариев или приложений, поскольку вы можете включить компонент отправки данных в состав более масштабного приложения по работе с большими объемами данных. В одних случаях этот код может иметь форму сценария или простой программы командной строки. В других случаях код может использоваться для интеграции обработки больших данных в бизнес-приложение или решение.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Данные, связанные с кластерами HDInsight Azure

Большинство типов кластеров HDInsight (Hadoop, HBase, Storm) поддерживают Data Lake Storage 2-го поколения в качестве репозитория хранения данных. Кластеры HDInsight обращаются к данным из BLOB-объектов хранилища Azure (WASB). Для повышения производительности можно скопировать данные из WASB в учетную запись Data Lake Storage 2-го поколения, связанную с кластером. Для копирования данных можно использовать указанные далее средства.

Ниже приведен список инструментов, которые можно использовать для приема данных, связанных с кластерами HDInsight.

|Средство | Руководство |
|---|--|
|Apache DistCp | [Использование средства DistCp для копирования данных между Azure Storage Blob и Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Инструмент AzCopy | [Передача данных с помощью AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Фабрика данных Azure | [Копирование данных в Azure Data Lake Storage 2-го поколения или из них с помощью фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Данные, хранящиеся в локальных кластерах Hadoop или кластерах Hadoop в IaaS

Большие объемы данных могут храниться в кластерах Hadoop, размещенных локально на компьютерах, использующих HDFS. Кластеры Hadoop могут быть развернуты локально или работать в кластере IaaS в Azure. К копированию таких данных в Azure Data Lake Storage 2-го поколения могут предъявляться требования, в зависимости от того, является ли эта операция одноразовой или повторяющейся. Существуют различные возможности выполнить их. Ниже приведен список альтернативных вариантов и связанные с ними компромиссы.

| Подход | Сведения | Преимущества | Рекомендации |
| --- | --- | --- | --- |
| Использование Фабрики данных Azure (ADF) для копирования данных напрямую из кластеров Hadoop в Azure Data Lake Storage 2-го поколения |[ADF поддерживает HDFS в качестве источника данных.](../../data-factory/connector-hdfs.md) |ADF реализована готовая поддержка HDFS, а также первоклассные инструменты комплексного управления и мониторинга. |Требуется развернуть шлюз управления данными в локальном кластере или кластере IaaS. |
| Использование Distcp для копирования данных из Hadoop в службу хранилища Azure. Затем копирование данных из службы хранилища Azure в Data Lake Storage 2-го поколения с помощью соответствующего механизма. |Скопировать данные из службы хранилища Azure в Data Lake Storage 2-го поколения можно с помощью следующих элементов. <ul><li>[Фабрика данных Azure](../../data-factory/copy-activity-overview.md)</li><li>[Средство AzCopy ](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp, запущенный в кластерах HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Можно использовать инструменты с открытым кодом. |Многоэтапный процесс с использованием нескольких технологий. |

### <a name="really-large-datasets"></a>Очень большие наборы данных

Для отправки наборов данных размером в несколько терабайт использование описанных выше методов иногда может быть медленным и затратным процессом. В таких случаях вы можете использовать Azure ExpressRoute.  

Azure ExpressRoute позволяет создавать закрытые подключения между центрами обработки данных Azure и вашей локальной инфраструктурой. Это надежный вариант для передачи больших объемов данных. Дополнительные сведения см. в [документации по Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Обработка данных

Данные, доступные в Data Lake Storage 2-го поколения, можно проанализировать с помощью поддерживаемых приложений для работы с большими данными. 

![Анализ данных в Data Lake Storage 2-го поколения](./media/data-lake-storage-data-scenarios/analyze-data.png "Анализ данных в Data Lake Storage 2-го поколения")

Ниже приведен список инструментов, которые можно использовать для выполнения заданий анализа данных, хранящихся в Azure Data Lake Storage 2-го поколения.

|Средство | Руководство |
|---|--|
|Azure HDInsight | [Использование Azure Data Lake Storage Gen2 с кластерами Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Хранилище Azure Data Lake Storage 2-го поколения](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Краткое руководство Анализ данных в Azure Data Lake Storage 2-го поколения с помощью Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Учебник. Извлечение, преобразование и загрузка данных с помощью Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Визуализация данных

Для создания визуальных представлений данных, хранящихся в Data Lake Storage 2-го поколения, можно использовать сочетание служб.

![Визуализация данных в Data Lake Storage 2-го поколения](./media/data-lake-storage-data-scenarios/visualize-data.png "Визуализация данных в Data Lake Storage 2-го поколения")

* Сначала с помощью [Фабрики данных Azure переместите данные из Data Lake Storage 2-го поколения в Хранилище данных SQL Azure](../../data-factory/copy-activity-overview.md).
* После этого вы можете [интегрировать Power BI с хранилищем данных SQL Azure](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) для создания визуального представления данных.

## <a name="download-the-data"></a>Скачивание данных

Возможно, вам потребуется скачать или переместить данные из Azure Data Lake Storage 2-го поколения в сценариях указанных далее.

* Перемещение данных в другие репозитории для взаимодействия с существующими конвейерами обработки данных. Например, можно переместить данные из Data Lake Storage 2-го поколения в базу данных SQL Azure или на локальный сервер SQL Server.

* Загрузка данных на локальный компьютер для обработки в средах IDE при создании прототипов приложений.

![Вывод данных из Data Lake Storage 2-го поколения](./media/data-lake-storage-data-scenarios/egress-data.png "Вывод данных из Data Lake Storage 2-го поколения")

Ниже приведен список инструментов, которые можно использовать для загрузки данных из Data Lake Storage 2-го поколения.

|Средство | Руководство |
|---|--|
|Фабрика данных Azure | [Действие копирования в Фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Использование средства DistCp для копирования данных между Azure Storage Blob и Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
