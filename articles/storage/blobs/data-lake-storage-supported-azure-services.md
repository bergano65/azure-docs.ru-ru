---
title: Службы Azure, поддерживающие Azure Data Lake Storage 2-го поколения | Документация Майкрософт
description: Узнайте, какие службы Azure интегрируются с Azure Data Lake Storage 2-го поколения
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b185b6e7706b2049f2821400db9d810588000419
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848219"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Службы Azure, поддерживающие Azure Data Lake Storage 2-го поколения

Службы Azure можно использовать для приема данных, выполнения анализа и создания визуальных представлений. Эта статья содержит список поддерживаемых служб Azure, раскрывает их уровень поддержки и содержит ссылки на статьи, которые помогут вам использовать эти службы с Azure Data Lake Storage 2-го поколения.

## <a name="supported-azure-services"></a>Поддерживаемые службы Azure

В этой таблице перечислены службы Azure, которые можно использовать с Azure Data Lake Storage 2-го поколения. Элементы в этих таблицах со временем будут меняться, так как поддержка постоянно расширяется.

> [!NOTE]
> Уровень поддержки относится только к поддержке службы с Data Lake Storage Gen 2.

|Служба Azure |Уровень поддержки |Azure AD |Общий ключ| Связанные статьи |
|---------------|-------------------|---|---|---|
|Фабрика данных Azure|Общедоступная версия|Да|Да|[Загрузка данных в Azure Data Lake Storage 2-го поколения с помощью Фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Общедоступная версия|Да|Да|[Использование с Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Краткое руководство. анализ данных в Azure Data Lake Storage 2-го поколения с помощью Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Руководство по Извлечение, преобразование и загрузка данных с помощью Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Учебник. доступ к данным Data Lake Storage 2-го поколения с помощью Azure Databricks с использованием Spark](data-lake-storage-use-databricks-spark.md)|
|концентратору событий Azure|Общедоступная версия|Нет|Да|[Сбор событий из Центров событий Azure в хранилище BLOB-объектов Azure или Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Сетка событий Azure|Общедоступная версия|Да|Да|[Руководство. Реализация шаблона Data Lake Capture для обновления разностной таблицы кирпичей данных](data-lake-storage-events.md)|
|Azure Logic Apps|Общедоступная версия|Нет|Да|[Обзор: Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Машинное обучение Azure|Общедоступная версия|Да|Да|[Доступ к данным в службах хранилища Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Общедоступная версия|Да|Да|[Краткое руководство. по созданию задания Stream Analytics с помощью портала Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Исходящий трафик в Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Общедоступная версия|Нет|Да|[Использование Azure Data Box для переноса данных из локального хранилища HDFS в службу хранилища Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Общедоступная версия|Да|Да|[Использование Azure Data Lake Storage Gen2 с кластерами Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Использование HDFS CLI в Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Руководство. Извлечение, преобразование и загрузка данных с помощью Apache Hive в Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|Центр Интернета вещей |Общедоступная версия|Нет|Да|[Использование маршрутизации сообщений центра Интернета вещей для отправки сообщений с устройства в облако в разные конечные точки](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Общедоступная версия|Да|Да|[Анализ данных в Data Lake Storage 2-го поколения с помощью Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (прежнее название — Хранилище данных SQL)|Общедоступная версия|Да|Да|[Использование с Azure синапсе Analytics (ранее — хранилище данных SQL)](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|Службы SQL Server Integration Services (SSIS)|Общедоступная версия|Да|Да|[Диспетчер подключений службы хранилища Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Data Explorer|Общедоступная версия|Да|Да|[Запрос данных в Azure Data Lake с помощью обозреватель данных Azure](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Когнитивный поиск Azure|Preview (Предварительный просмотр)|Да|Да|[Индексирование и поиск Azure Data Lake Storage 2-го поколения документов (Предварительная версия)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Сеть доставки содержимого Azure|Еще не поддерживается|Неприменимо|Неприменимо|[Индексирование и поиск Azure Data Lake Storage 2-го поколения документов (Предварительная версия)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>См. также раздел

- [Известные проблемы с Azure Data Lake Storage 2-го поколения](data-lake-storage-known-issues.md)
- [Функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-blob-storage-features.md)
- [Платформы с открытым кодом, поддерживающие Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-open-source-platforms.md)
- [Multi-protocol access on Azure Data Lake Storage (preview)](data-lake-storage-multi-protocol-access.md) (Доступ с использованием нескольких протоколов на Azure Data Lake Storage (предварительная версия))