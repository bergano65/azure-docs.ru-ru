---
title: Интеграция Azure Data Lake Storage 2-го поколения с другими службами Azure | Документация Майкрософт
description: Сведения об интеграции Azure Data Lake Storage 2-го поколения с другими службами Azure.
documentationcenter: ''
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: nitinme
ms.openlocfilehash: 7bc4889403e1d52cfa3b18792a554f0faf605132
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885545"
---
# <a name="integrate-azure-data-lake-storage-gen2-with-other-azure-services"></a>Интеграция Azure Data Lake Storage 2-го поколения с другими службами Azure

С помощью служб Azure вы можете принимать, анализировать и визуализировать данные в учетной записи хранения Azure Data Lake Storage 2-го поколения. Выберите службы, которые лучше всего подойдут для выполнения требуемых задач.

## <a name="ingest-data-into-your-data-lake"></a>Прием данных в Data Lake

Использование этих служб поможет наполнить Data Lake данными.

### <a name="azure-data-factory"></a>Фабрика данных Azure

Используйте службу [Фабрика данных Azure](https://azure.microsoft.com/services/data-factory/) для интеграции данных из следующих источников.

* Таблицы Azure
* Базы данных SQL Azure
* Хранилище данных SQL Azure
* Azure Storage Blob
* Локальные базы данных

См. статью [Копирование данных в Azure Data Lake Storage Gen2 и из него с помощью фабрики данных Azure](../../data-factory/connector-azure-data-lake-store.md).

## <a name="analyze-and-visualize-data-in-your-data-lake"></a>Анализ и визуализация данных в Data Lake

В качестве конечной точки хранилища в этих службах может использоваться Data Lake Storage 2-го поколения.

### <a name="azure-hdinsight"></a>Azure HDInsight

Можно подготовить кластер [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/), который использует Data Lake Storage 2-го поколения в качестве хранилища, соответствующего требованиям HDFS. В этом выпуске для кластеров Hadoop и Storm в Windows и Linux вы можете использовать Data Lake Storage 2-го поколения только в качестве дополнительного хранилища. В этих кластерах по-прежнему используется хранилище Azure (WASB) в качестве хранилища по умолчанию. Однако для кластеров HBase в Windows и Linux можете использовать Data Lake Storage 2-го поколения в качестве хранилища по умолчанию либо дополнительного хранилища.

См. статью [Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).

### <a name="azure-data-lake-analytics"></a>Аналитика озера данных Azure

Чтобы работать с большими данными в облачных масштабах, можете использовать [Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-overview.md). Служба динамически выделяет ресурсы и позволяет анализировать эксабайты данных. Служба Data Lake Analytics оптимизирована на использование Azure Data Lake Storage 2-го поколения в качестве источника данных. 

[Начало работы с Azure Data Lake Analytics с помощью портала Azure](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="copy-data-to-other-repositories"></a>Копирование данных в другие репозитории

Эти службы предназначены для копирования данных из Data Lake и их размещения в других репозиториях.

### <a name="sql-data-warehouse"></a>Хранилище данных SQL

С помощью PolyBase вы можете загружать данные из Data Lake Storage 2-го поколения в Хранилище данных SQL. Дополнительные сведения см. в статье [Загрузка данных из Azure Data Lake Storage 2-го поколения в хранилище данных SQL](../../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="see-also"></a>См. также

* [Общие сведения об Azure Data Lake Storage 2-го поколения](data-lake-storage-introduction.md)
* [Использование Azure Data Lake Storage 2-го поколения для обеспечения соответствия требованиям больших данных](data-lake-storage-data-scenarios.md)
