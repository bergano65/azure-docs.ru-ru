---
title: Службы Azure, поддерживающие данные Azure Data Lake Storage Gen2 Документы Майкрософт
description: Узнайте, какие службы Azure интегрируются с системой хранения данных Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: edfcb1d0bfc87c84620b13ed26ec681fef32f1e3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878329"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Службы Azure, поддерживающие хранение озер данных Azure Gen2

Службы Azure можно использовать для поимки данных, проведения аналитики и создания визуальных представлений. В этой статье приводится список поддерживаемых служб Azure, раскрывается уровень их поддержки и предоставляется ссылки на статьи, которые помогают вам использовать эти службы с Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Поддерживаемые службы Azure

В этой таблице перечислены службы Azure, которые можно использовать с помощью Хранилища озер Лазурных Данных Gen2. Элементы, которые отображаются в этих таблицах, будут меняться с течением времени по мере расширения поддержки.

> [!NOTE]
> Уровень поддержки относится только к тому, как служба поддерживается Data Lake Storage Gen 2.

|Служба Azure |Уровень поддержки |Связанные статьи |
|---------------|-------------------|---|
|Фабрика данных Azure|Общедоступная версия|[Загрузка данных в Azure Data Lake Storage 2-го поколения с помощью Фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Общедоступная версия|[Использование с Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Быстрый запуск: Анализ данных в Azure Data Lake Storage2 с помощью Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Учебник: Извлекайте, преобразовывали и загружали данные с помощью Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Учебник: Доступ к данным хранения данных озера Gen2 с Azure Databricks с помощью Spark](data-lake-storage-use-databricks-spark.md)|
|концентратору событий Azure|Общедоступная версия|[Сбор событий из Центров событий Azure в хранилище BLOB-объектов Azure или Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Сетка событий Azure|Общедоступная версия|[Учебник: Реализация шаблона захвата озера данных для обновления таблицы Дельты Databricks](data-lake-storage-events.md)|
|Azure Logic Apps|Общедоступная версия|[Обзор: Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Машинное обучение Azure|Общедоступная версия|[Доступ к данным в службах хранения данных Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Общедоступная версия|[Краткое руководство по созданию задания Stream Analytics с помощью портала Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Выход на озеро Лазурные данные Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Общедоступная версия|[Используйте поле данных Azure для переноса данных из входной службы HDFS в хранилище Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Общедоступная версия|[Используйте хранилище данных Azure Data Lake Gen2 с кластерами Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Использование HDFS CLI в Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Учебник: Извлекайте, преобразуйте и загружайте данные с помощью Apache Hive на Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|Центр Интернета вещей |Общедоступная версия|[Используйте разгром сообщений IoT Hub для отправки сообщений от устройства в облако в различные конечные точки](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Общедоступная версия|[Анализ данных в Data Lake Storage Gen2 с помощью Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|Хранилище данных SQL|Общедоступная версия|[Использование с Хранилищем данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|Службы SQL Server Integration Services (SSIS)|Общедоступная версия|[Менеджер подключения к хранению Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Когнитивный поиск Azure|Предварительный просмотр|[Документы индекса и поиска Azure Data Lake Storage Gen2 (предварительный просмотр)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Data Explorer|Предварительный просмотр|[Данные о запросах в Azure Data Lake с помощью Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Сеть доставки содержимого Azure|Еще не поддерживается|[Документы индекса и поиска Azure Data Lake Storage Gen2 (предварительный просмотр)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>См. также

- [Известные проблемы с Azure Data Lake Storage 2-го поколения](data-lake-storage-known-issues.md)
- [Функции хранения Blob доступны в Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Платформы с открытым исходным кодом, поддерживающие данные Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Multi-protocol access on Azure Data Lake Storage (preview)](data-lake-storage-multi-protocol-access.md) (Доступ с использованием нескольких протоколов на Azure Data Lake Storage (предварительная версия))