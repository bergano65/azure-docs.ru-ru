---
title: 'Подключение к различным источникам данных из Azure Databricks '
description: Узнайте, как подключиться к базе данных SQL Azure, Azure Data Lake Store, хранилищу BLOB-объектов, Cosmos DB, концентраторам событий и хранилищу данных SQL Azure из Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: c77d1d1a66d3ee92f5ad3f2016d2160831fa3ad9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299310"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Подключение к источникам данных из Azure Databricks

Эта статья содержит ссылки на все источники данных, которые можно подключать к Azure Databricks. Статьи по этим ссылкам содержат примеры, которые позволяют извлечь в кластер Azure Databricks данные из источников данных Azure (таких как хранилище больших двоичных объектов Azure, Центры событий Azure, и т. д.) и запустить задания аналитики для этих данных. 

## <a name="prerequisites"></a>Предварительные требования

* Вам потребуется рабочая область Azure Databricks и кластер Spark. Выполните инструкции из статьи [Начало работы с Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Источники данных для Azure Databricks

В следующем списке перечислены источники данных в Azure, которые можно использовать с Azure Databricks. Полный список поддерживаемых в Azure Databricks источников данных можно найти в статье [Spark Data Sources](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) (Источники данных Spark).

- [База данных SQL Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Это ссылка на API DataFrame для подключения к базам данных SQL с помощью JDBC и сведения об управлении параллелизмом при операциях чтения через интерфейс JDBC. Эта статья содержит подробные примеры использования API Scala, а также краткие примеры для Python и Spark SQL (в конце).
- [Azure Data Lake Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)

    Здесь представлены примеры по использованию субъекта-службы Azure Active Directory для аутентификации в Data Lake Store. Также в статье объясняется, как получить доступ из Azure Databricks к данным в Data Lake Store.

- [Хранилище BLOB-объектов Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Здесь представлены примеры прямого доступа к хранилищу BLOB-объектов из Azure Databricks с использованием ключа доступа или SAS для заданного контейнера. Также статья содержит информацию о доступе к хранилищу BLOB-объектов из Azure Databricks с помощью RDD API.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Здесь представлены инструкции по использованию [соединителя Azure Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) из Databricks Azure для доступа к данным в Azure Cosmos DB.

- [Центры событий Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Здесь представлены инструкции по использованию [соединителя Spark для Центров событий Azure](https://github.com/Azure/azure-event-hubs-spark) из Databricks Azure для доступа к данным в Центрах событий Azure.

- [Хранилище данных Azure SQL](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Здесь представлены инструкции о том, как использовать соединитель хранилища данных SQL Azure для подключения из Azure Databricks.
    

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения об источниках, которые Azure Databricks поддерживает для импорта данных, см. в [этой статье](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


