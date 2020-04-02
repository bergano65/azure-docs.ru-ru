---
title: 'Подключение к различным источникам данных из Azure Databricks '
description: Узнайте, как подключиться к базе данных Azure S'L, магазину озер данных Azure, хранению кабы, Cosmos DB, концентратам событий и хранилищем данных Azure S-L от Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 79a821a4c8fe4cb2d048f0dcb0a6e091462a1779
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548794"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Подключение к источникам данных из Azure Databricks

Эта статья содержит ссылки на все источники данных, которые можно подключать к Azure Databricks. Статьи по этим ссылкам содержат примеры, которые позволяют извлечь в кластер Azure Databricks данные из источников данных Azure (таких как хранилище больших двоичных объектов Azure, Центры событий Azure, и т. д.) и запустить задания аналитики для этих данных. 

## <a name="prerequisites"></a>Предварительные требования

* Вам потребуется рабочая область Azure Databricks и кластер Spark. Выполните инструкции из статьи [Начало работы с Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Источники данных для Azure Databricks

В следующем списке перечислены источники данных в Azure, которые можно использовать с Azure Databricks. Полный список поддерживаемых в Azure Databricks источников данных можно найти в статье [Spark Data Sources](/azure/databricks/data/data-sources/index) (Источники данных Spark).

- [База данных Azure S'L](/azure/databricks/data/data-sources/sql-databases)

    Это ссылка на API DataFrame для подключения к базам данных SQL с помощью JDBC и сведения об управлении параллелизмом при операциях чтения через интерфейс JDBC. Эта статья содержит подробные примеры использования API Scala, а также краткие примеры для Python и Spark SQL (в конце).
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    В этой ссылке приводятся примеры использования принципа службы активного каталога Azure для проверки подлинности с помощью хранилища озер данных Azure. В нем также содержатся инструкции о том, как получить доступ к данным в хранилище озер Лазурных данных из Azure Databricks.

- [Хранение Azure Blob](/azure/databricks/data/data-sources/azure/azure-storage)

    Здесь представлены примеры прямого доступа к хранилищу BLOB-объектов из Azure Databricks с использованием ключа доступа или SAS для заданного контейнера. Также статья содержит информацию о доступе к хранилищу BLOB-объектов из Azure Databricks с помощью RDD API.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Здесь представлены инструкции по использованию [соединителя Azure Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) из Databricks Azure для доступа к данным в Azure Cosmos DB.

- [Центры событий Azure](/azure/event-hubs/event-hubs-spark-connector)

    Здесь представлены инструкции по использованию [соединителя Spark для Центров событий Azure](https://github.com/Azure/azure-event-hubs-spark) из Databricks Azure для доступа к данным в Центрах событий Azure.

- [Хранилище данных SQL Azure](/azure/synapse-analytics/sql-data-warehouse/)

    Здесь представлены инструкции о том, как использовать соединитель хранилища данных SQL Azure для подключения из Azure Databricks.
    

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать об источниках, из которых можно импортировать данные в Azure Databricks, [см.](/azure/databricks/data/data-sources/index)


