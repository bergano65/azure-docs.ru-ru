---
title: Интеграция Azure Data Lake Storage со службами Azure | Документация Майкрософт
description: Узнайте, какие службы Azure интегрируются с Azure Data Lake Storage 2-го поколения.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.openlocfilehash: 3add7e31568831e4c2de6901791d131ff01f43bb
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588113"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>Интеграция Azure Data Lake Storage со службами Azure

Службы Azure можно использовать для приема данных, выполнения анализа и создания визуальных представлений. В этой статье содержится список поддерживаемых служб Azure и приведены ссылки на статьи, которые помогут вам использовать эти службы с Azure Data Lake Storage 2-го поколения.

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Службы Azure, которые поддерживают Azure Data Lake Storage 2-го поколения

В следующей таблице перечислены службы Azure, которые поддерживают Azure Data Lake Storage 2-го поколения.

| Служба Azure |  Связанные статьи |
|---------------|-------------------|
|Фабрика данных Azure | [Загрузка данных в Azure Data Lake Storage 2-го поколения с помощью Фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [Использование с Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Краткое руководство. анализ данных в Azure Data Lake Storage 2-го поколения с помощью Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Учебник. Извлечение, преобразование и загрузка данных с помощью Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Учебник. доступ к данным Data Lake Storage 2-го поколения с помощью Azure Databricks с использованием Spark](data-lake-storage-use-databricks-spark.md) |
|Запись концентраторов событий Azure| [Сбор событий с помощью концентраторов событий Azure в хранилище BLOB-объектов Azure или Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps | [Обзор. что такое Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Машинное обучение Azure|[Доступ к данным в службах хранилища Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data)|
|Поиск Azure (Предварительная версия)| [Поиск хранилища BLOB-объектов с помощью службы поиска Azure](https://docs.microsoft.com/azure/search/search-blob-storage-integration)|
|Azure Stream Analytics| [Краткое руководство по созданию задания Stream Analytics с помощью портала Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Исходящий трафик в Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|Data Box|  [Использование Azure Data Box для переноса данных из локального хранилища HDFS в службу хранилища Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [Использование Azure Data Lake Storage Gen2 с кластерами Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Использование интерфейса командной строки HDFS с Data Lake Storage 2-го поколения](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Руководство. Извлечение, преобразование и загрузка данных с помощью Apache Hive в Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md) |
|Центр Интернета вещей | [Использование маршрутизации сообщений центра Интернета вещей для отправки сообщений с устройства в облако в разные конечные точки](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [Анализ данных в Data Lake Storage 2-го поколения с помощью Power BI](data-lake-storage-use-power-bi.md) |
|Хранилище данных SQL. | [Использование с хранилищем данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS); | [Диспетчер подключений службы хранилища Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о средствах, которые можно использовать для обработки данных в Data Lake. Дополнительные сведения см. [в разделе использование Azure Data Lake Storage 2-го поколения для обработки больших данных](data-lake-storage-data-scenarios.md).

- Узнайте больше о Data Lake Storage 2-го поколения и о том, как приступить к работе. См. статью [Общие сведения о Azure Data Lake Storage 2-го поколения](data-lake-storage-introduction.md)