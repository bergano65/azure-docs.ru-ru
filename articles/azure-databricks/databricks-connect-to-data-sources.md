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
ms.openlocfilehash: 932031f545ccc75cb12f368f5c7894c1b5e454cd
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091685"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Подключение к источникам данных из Azure Databricks

Эта статья содержит ссылки на все источники данных, которые можно подключать к Azure Databricks. Статьи по этим ссылкам содержат примеры, которые позволяют извлечь в кластер Azure Databricks данные из источников данных Azure (таких как хранилище больших двоичных объектов Azure, Центры событий Azure, и т. д.) и запустить задания аналитики для этих данных. 

## <a name="prerequisites"></a>предварительным требованиям

* Вам потребуется рабочая область Azure Databricks и кластер Spark. Выполните инструкции из статьи [Начало работы с Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Источники данных для Azure Databricks

В следующем списке перечислены источники данных в Azure, которые можно использовать с Azure Databricks. Полный список поддерживаемых в Azure Databricks источников данных можно найти в статье [Spark Data Sources](/azure/databricks/data/data-sources/index) (Источники данных Spark).

- [База данных SQL Azure](/azure/databricks/data/data-sources/sql-databases)

    Это ссылка на API DataFrame для подключения к базам данных SQL с помощью JDBC и сведения об управлении параллелизмом при операциях чтения через интерфейс JDBC. Эта статья содержит подробные примеры использования API Scala, а также краткие примеры для Python и Spark SQL (в конце).
- [Хранилище озера данных Azure](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Эта ссылка содержит примеры использования субъекта-службы Azure Active Directory для проверки подлинности с помощью Azure Data Lake Storage. В нем также приводятся инструкции по доступу к данным в Azure Data Lake Storage из Azure Databricks.

- [Хранилище BLOB-объектов Azure](/azure/databricks/data/data-sources/azure/azure-storage)

    Здесь представлены примеры прямого доступа к хранилищу BLOB-объектов из Azure Databricks с использованием ключа доступа или SAS для заданного контейнера. Также статья содержит информацию о доступе к хранилищу BLOB-объектов из Azure Databricks с помощью RDD API.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Здесь представлены инструкции по использованию [соединителя Azure Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) из Databricks Azure для доступа к данным в Azure Cosmos DB.

- [Центры событий Azure](/azure/databricks/data/data-sources/azure/eventhubs-connector)

    Здесь представлены инструкции по использованию [соединителя Spark для Центров событий Azure](https://github.com/Azure/azure-event-hubs-spark) из Databricks Azure для доступа к данным в Центрах событий Azure.

- [Хранилище данных Azure SQL](/azure/databricks/data/data-sources/azure/sql-data-warehouse)

    Здесь представлены инструкции о том, как использовать соединитель хранилища данных SQL Azure для подключения из Azure Databricks.
    

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об источниках, которые Azure Databricks поддерживает для импорта данных, см. в [этой статье](/azure/databricks/data/data-sources/index).


