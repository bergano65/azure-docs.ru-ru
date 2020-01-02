---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: e6a1e65f253f33257a0e218f4d347740c07581cc
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796211"
---
Фабрика данных Azure поддерживает указанные ниже действия преобразования, которые вы можете добавлять в конвейеры как по отдельности, так и в связи с другим действием.

| Действия по преобразованию данных | Вычислительная среда |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Потоковая передача Hadoop](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Создание прогнозирующих конвейеров с помощью машинного обучения Azure и фабрики данных Azure](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |Azure |
| [Хранимая процедура](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |Azure SQL, хранилище данных Azure SQL или SQL Server |
| [Аналитика озера данных U-SQL](../articles/data-factory/v1/data-factory-usql-activity.md) |Аналитика озера данных Azure |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |HDInsight [Hadoop] или пакетная служба Azure |

> [!NOTE]
> Действие MapReduce можно использовать для запуска программ Spark в кластере HDInsight Spark. Дополнительные сведения см. в разделе [Вызов программ Spark из фабрики данных](../articles/data-factory/v1/data-factory-spark.md).
> Можно создать настраиваемое действие для выполнения сценариев R в кластере HDInsight, где установлена среда R. Ознакомьтесь с примером в репозитории GitHub [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) (Запуск сценария R с помощью фабрики данных Azure).
> 
> 

