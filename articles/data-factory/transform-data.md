---
title: 'Преобразование данных с помощью фабрики данных Azure '
description: Узнайте, как преобразовать или обработать данные в фабрике данных Azure с помощью Hadoop, Машинного обучения Azure или Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 40157228e3bb2ab7625d703a25dd1e820ca1bc07
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74703584"
---
# <a name="transform-data-in-azure-data-factory"></a>Преобразование данных в фабрике данных Azure
> [!div class="op_single_selector"]
> * [Поток данных сопоставления](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Потоковая передача HDInsight](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Машинное обучение](transform-data-using-machine-learning.md) 
> * [Хранимая процедура](transform-data-using-stored-procedure.md)
> * [Аналитика озера данных U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks Notebook](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [Пользовательские действия .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Краткое описание
В этой статье описываются действия по преобразованию данных в фабрике данных Azure, которые можно использовать для преобразования и обработки необработанных данных в прогнозы и аналитические сведения в масштабе. Действие преобразования выполняется в вычислительной среде, например Azure Databricks или Azure HDInsight. Статья содержит ссылки на статьи с подробными сведениями о каждом действии преобразования.

Фабрика данных поддерживает указанные ниже действия преобразования, которые вы можете добавлять в [конвейеры](concepts-pipelines-activities.md) как по отдельности, так и в цепочке с другим действием.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Встроенное преобразование в фабрике данных Azure с помощью потоков данных

### <a name="mapping-data-flows"></a>Сопоставление потоков данных

Сопоставление потоков данных — это визуально спроектированные преобразования данных в фабрике данных Azure. Потоки данных позволяют инженерам данных разрабатывать логику преобразования графических данных без написания кода. Результирующие потоки данных выполняются в виде действий в конвейерах фабрики данных Azure, использующих масштабируемые кластеры Spark. Действия потока данных могут быть реализованы с помощью существующих возможностей планирования фабрики данных, управления, потоков и мониторинга. Дополнительные сведения см. в разделе [сопоставление потоков данных](concepts-data-flow-overview.md).

### <a name="wrangling-data-flows"></a>Потоки данных структурирование

Потоки данных структурирование в фабрике данных Azure позволяют итеративно выполнять подготовку данных без кода в масштабе облака. Потоки данных структурирование интегрируются с [Power Query Online](https://docs.microsoft.com/power-query/) и делают функции Power Query M доступными для структурирование данных в облачном масштабировании с помощью выполнения Spark. Дополнительные сведения см. в разделе [структурирование Data](wrangling-data-flow-overview.md)Flows.

## <a name="external-transformations"></a>Внешние преобразования

При необходимости можно вручную преобразовать код и управлять внешней средой вычислений самостоятельно.

### <a name="hdinsight-hive-activity"></a>Действие Hive HDInsight
Действие Hive HDInsight в конвейере фабрики данных выполняет запросы Hive к вашему собственному кластеру HDInsight или кластеру HDInsight по запросу под управлением Windows или Linux. Дополнительные сведения об этом действии см. в статье [Преобразование данных с помощью действия Hadoop Hive в фабрике данных Azure](transform-data-using-hadoop-hive.md). 

### <a name="hdinsight-pig-activity"></a>Действие Pig HDInsight
Действие Pig HDInsight в конвейере фабрики данных выполняет запросы Pig к вашему собственному кластеру HDInsight или кластеру HDInsight по запросу под управлением Windows или Linux. Дополнительные сведения об этом действии см. в статье [Преобразование данных с помощью действия Hadoop Pig в фабрике данных Azure](transform-data-using-hadoop-pig.md). 

### <a name="hdinsight-mapreduce-activity"></a>Действие MapReduce HDInsight
Действие MapReduce HDInsight в конвейере фабрики данных выполняет программы MapReduce для вашего собственного кластера HDInsight или кластера HDInsight по запросу под управлением Windows или Linux. Дополнительные сведения об этом действии см. в статье [Преобразование данных с помощью действия MapReduce в фабрике данных Azure](transform-data-using-hadoop-map-reduce.md).

### <a name="hdinsight-streaming-activity"></a>Действие потоковой передачи HDInsight
Действие потоковой передачи HDInsight в конвейере фабрики данных выполняет программы потоковой передачи Hadoop для вашего собственного кластера HDInsight или кластера HDInsight по запросу под управлением Windows или Linux. Дополнительные сведения об этом действии см. в разделе [Потоковая активность Hadoop](transform-data-using-hadoop-streaming.md).

### <a name="hdinsight-spark-activity"></a>Действие HDInsight Spark
Действие HDInsight Spark в конвейере фабрики данных выполняет программы Spark в вашем кластере HDInsight. Дополнительные сведения см. в разделе [Вызов программ Spark из фабрики данных](transform-data-using-spark.md). 

### <a name="machine-learning-activities"></a>Действия машинного обучения
Фабрика данных Azure позволяет легко создавать конвейеры, в которых для прогнозной аналитики используется опубликованная веб-служба Машинного обучения Azure. С помощью [действия выполнения пакета](transform-data-using-machine-learning.md) в конвейере фабрики данных Azure можно вызывать веб-службу Машинного обучения Azure, чтобы создавать прогнозы по данным в пакете.

Со временем прогнозные модели в оценивающих экспериментах машинного обучения потребуют повторного обучения с помощью новых входных наборов данных. Когда повторное обучение будет завершено, вам потребуется обновить веб-службу оценки на основании обновленной модели машинного обучения. Чтобы обновить веб-службу с помощью заново обученной модели, можно использовать [действие обновления ресурса](update-machine-learning-models.md).  

Дополнительные сведения об этих действиях машинного обучения см. в разделе [Создание прогнозных конвейеров с помощью действий машинного обучения Azure](transform-data-using-machine-learning.md). 

### <a name="stored-procedure-activity"></a>Действие хранимой процедуры
C помощью действия хранимой процедуры SQL Server в конвейере фабрики данных можно вызвать хранимую процедуру из одного из следующих хранилищ данных: база данных SQL Azure, хранилище данных SQL Azure, база данных SQL Server вашего предприятия или виртуальная машина Azure. Дополнительные сведения см. в статье [Преобразование данных с помощью действия хранимой процедуры SQL Server в фабрике данных Azure](transform-data-using-stored-procedure.md).  

### <a name="data-lake-analytics-u-sql-activity"></a>Действие U-SQL Data Lake Analytics
Действие U-SQL Data Lake Analytics запускает скрипт U-SQL для кластера Azure Data Lake Analytics. Дополнительные сведения см. в статье [Преобразование данных с помощью сценариев U-SQL в Azure Data Lake Analytics](transform-data-using-data-lake-analytics.md). 

### <a name="databricks-notebook-activity"></a>Действие Notebook в Databricks

Действие Azure Databricks записной книжке в конвейере фабрики данных запускает записную книжку "кирпичы" в рабочей области Azure Databricks. Azure Databricks — это управляемая платформа для запуска Apache Spark. См. раздел [Преобразование данных с помощью записной книжки Databricks](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Действие JAR в Databricks

Действие Jar в Azure Databricks в конвейере Фабрики данных позволяет запустить файл Spark Jar в кластере Azure Databricks. Azure Databricks — это управляемая платформа для запуска Apache Spark. См. раздел [Преобразование данных с помощью выполнения действий Jar в Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Действие Python в Databricks

Действие Python в Azure Databricks в конвейере Фабрики данных позволяет запустить файл Python в кластере Azure Databricks. Azure Databricks — это управляемая платформа для запуска Apache Spark. См. раздел [Преобразование данных с помощью выполнения действий Python в Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Настраиваемое действие
Если вам нужно преобразовать данные способом, который не поддерживается фабрикой данных Azure, то можно создать настраиваемое действие с собственной логикой обработки данных и использовать это действие в конвейере. Можно настроить запуск настраиваемого действия .NET с помощью пакетной службы Azure или кластера HDInsight. Дополнительные сведения см. в разделе [Использование настраиваемых действий в конвейере фабрики данных Azure](transform-data-using-dotnet-custom-activity.md). 

Можно создать настраиваемое действие для выполнения сценариев R в кластере HDInsight, где установлена среда R. Ознакомьтесь с примером в репозитории GitHub [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) (Запуск сценария R с помощью фабрики данных Azure). 

### <a name="compute-environments"></a>Вычислительные среды
Вы создаете связанную службу для среды вычислений, а затем используете эту службу при определении действия преобразования. Фабрика данных поддерживает вычислительные среды двух типов. 

- **По требованию**: в этом случае вычислительная среда полностью управляется фабрикой данных. Среда автоматически создается службой фабрики данных перед отправкой задания для обработки данных и удаляется после его выполнения. Вы можете настраивать и изменять для вычислительной среды "по требованию" детализированные параметры выполнения задания, управления кластером и действий начальной загрузки. 
- **Собственная**— в этом случае вы регистрируете собственную вычислительную среду (например, кластер HDInsight) и используете ее в качестве связанной службы в фабрике данных. Вы будете управлять средой вычислений, а служба фабрики данных — использовать ее для выполнения действий. 

В статье [Связанные службы вычислений](compute-linked-services.md) описывается, какие службы вычислений поддерживает фабрика данных. 

## <a name="next-steps"></a>Дальнейшие действия
Пример использования действия преобразования см. в руководстве [Преобразование данных в облаке с помощью действия Spark в фабрике данных Azure](tutorial-transform-data-spark-powershell.md).
