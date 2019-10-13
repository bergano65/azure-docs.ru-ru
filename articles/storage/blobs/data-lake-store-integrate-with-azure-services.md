---
title: Интеграция Azure Data Lake Storage со службами Azure | Документация Майкрософт
description: Узнайте, какие службы Azure интегрируются с Azure Data Lake Storage 2-го поколения.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.openlocfilehash: e073c02f19a9add37e684a76839a620af0d07ec0
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301325"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>Интеграция Azure Data Lake Storage со службами Azure

Службы Azure можно использовать для приема данных, выполнения анализа и создания визуальных представлений. В этой статье содержится список поддерживаемых служб Azure и приведены ссылки на статьи, которые помогут вам использовать эти службы с Azure Data Lake Storage 2-го поколения.

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Службы Azure, которые поддерживают Azure Data Lake Storage 2-го поколения

В следующей таблице перечислены службы Azure, которые поддерживают Azure Data Lake Storage 2-го поколения.

| Служба Azure |  Связанные статьи |
|---------------|-------------------|
|Фабрика данных Azure | [Загрузка данных в Azure Data Lake Storage 2-го поколения с помощью Фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [Использование с Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Краткое руководство Анализ данных в Azure Data Lake Storage 2-го поколения с помощью Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Учебник. Извлечение, преобразование и загрузка данных с помощью Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Учебник. Доступ к Data Lake Storage 2-го поколения данным Azure Databricks с помощью Spark @ no__t-0 |
|Запись концентраторов событий Azure| [Сбор событий с помощью концентраторов событий Azure в хранилище BLOB-объектов Azure или Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps | [Обзор. что такое Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Поиск Azure | [Поиск хранилища BLOB-объектов с помощью службы поиска Azure](https://docs.microsoft.com/azure/search/search-blob-storage-integration)|
|Azure Stream Analytics| [Исходящий трафик в Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|датабокс|  [Использование Azure Data Box для переноса данных из локального хранилища HDFS в службу хранилища Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [Использование Azure Data Lake Storage Gen2 с кластерами Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Использование интерфейса командной строки HDFS с Data Lake Storage 2-го поколения](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Учебник. Извлечение, преобразование и загрузка данных с помощью Apache Hive в Azure HDInsight @ no__t-0 |
|Центр Интернета вещей | [Использование маршрутизации сообщений центра Интернета вещей для отправки сообщений с устройства в облако в разные конечные точки](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [Анализ данных в Data Lake Storage 2-го поколения с помощью Power BI](data-lake-storage-use-power-bi.md) |
|Хранилище данных SQL | [Использование с хранилищем данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS); | [Диспетчер подключений службы хранилища Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>Следующие шаги

- Узнайте больше о средствах, которые можно использовать для обработки данных в Data Lake. Дополнительные сведения см. [в разделе использование Azure Data Lake Storage 2-го поколения для обработки больших данных](data-lake-storage-data-scenarios.md).

- Узнайте больше о Data Lake Storage 2-го поколения и о том, как приступить к работе. См. статью [Общие сведения о Azure Data Lake Storage 2-го поколения](data-lake-storage-introduction.md)