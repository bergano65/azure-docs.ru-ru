---
title: Преобразование данных с помощью активности Hadoop MapReduce
description: Узнайте, как обрабатывать данные путем выполнения программ Hadoop MapReduce в кластере Azure HDInsight из фабрики данных Azure.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 5d38e3126442bcf34c96cead2b2ea59507b50b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912867"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Преобразование данных с помощью действия Hadoop MapReduce в фабрике данных Azure

> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-map-reduce.md)
> * [Текущая версия](transform-data-using-hadoop-map-reduce.md)

Действие MapReduce HDInsight в [конвейере](concepts-pipelines-activities.md) Фабрики данных вызывает программу MapReduce для [вашего](compute-linked-services.md#azure-hdinsight-linked-service) кластера HDInsight или кластера HDInsight [по запросу](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Данная статья основана на материалах статьи о [действиях преобразования данных](transform-data.md) , в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования.

Если вы не знакомы с фабрикой данных Azure, сначала ознакомьтесь со статьей [Введение в фабрику данных Azure](introduction.md) и руководством [Преобразование данных в облаке с помощью действия Spark в фабрике данных Azure](tutorial-transform-data-spark-powershell.md).

Дополнительные сведения о выполнении скриптов Pig и Hive в кластере HDInsight из конвейера с помощью действий Pig и Hive в HDInsight см. в статьях о действии [Pig](transform-data-using-hadoop-pig.md) и [Hive](transform-data-using-hadoop-hive.md).

## <a name="syntax"></a>Синтаксис

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Сведения о синтаксисе

| Свойство          | Описание                              | Обязательно |
| ----------------- | ---------------------------------------- | -------- |
| name              | Имя действия.                     | Да      |
| description       | Текст, описывающий, для чего используется действие | нет       |
| type              | Для действия MapReduce Activity используется тип действия HDinsightMapReduce. | Да      |
| linkedServiceName | Ссылка на кластер HDInsight, зарегистрированный в качестве связанной службы в фабрике данных. Дополнительные сведения об этой связанной службе см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md). | Да      |
| className         | Имя класса для выполнения.         | Да      |
| jarLinkedService  | Ссылки на связанные службы хранилища Azure, используемые для хранения файлов Jar. Если не указать эту связанную службу, будет использоваться связанная служба хранилища Azure, определенная в связанной службе HDInsight. | нет       |
| jarFilePath       | Укажите путь к файлам Jar, которые хранятся в службе хранилища Azure, на который ссылается jarLinkedService. В имени файла учитывается регистр знаков. | Да      |
| jarlibs           | Массив строк пути к файлам библиотеки Jar, на которые ссылается задание, хранящееся в службе хранилища Azure, которая, в свою очередь, определена в свойстве jarLinkedService. В имени файла учитывается регистр знаков. | нет       |
| getDebugInfo      | Указывает, когда файлы журнала копируются в службу хранилища Azure, используемую кластером HDInsight или определенную jarLinkedService. Допустимые значения: None, Always или Failure. Значение по умолчанию: None. | нет       |
| аргументы         | Указывает массив аргументов для задания Hadoop. Аргументы передаются в качестве аргументов командной строки в каждую задачу. | нет       |
| defines           | Параметры в виде пары "ключ — значение", ссылки на которые указываются в скрипте Hive. | нет       |



## <a name="example"></a>Пример
С помощью действия MapReduce можно выполнить JAR-файл MapReduce в кластере HDInsight. В приведенном ниже образце определения конвейера JSON действие HDInsight настроено на запуск JAR-файла Mahout.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
Вы можете указать необходимые аргументы для программы MapReduce в разделе **arguments**. Во время выполнения вы увидите несколько дополнительных аргументов (например, mapreduce.job.tags) платформы MapReduce. Чтобы отличать свои аргументы от аргументов MapReduce, вы можете использовать параметр и значение в качестве аргументов, как показано в следующем примере (-s, --input, --output и т. д — параметры, за которыми сразу следуют их значения).

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими ссылками, в которых описаны способы преобразования данных другими способами:

* [Действие U-SQL](transform-data-using-data-lake-analytics.md)
* [Действие Hive](transform-data-using-hadoop-hive.md)
* [Свинья деятельность](transform-data-using-hadoop-pig.md)
* [Действие потоковой передачи Hadoop](transform-data-using-hadoop-streaming.md)
* [Активность искры](transform-data-using-spark.md)
* [пользовательская деятельность .NET](transform-data-using-dotnet-custom-activity.md)
* [Деятельность по выполнению пакета машинного обучения](transform-data-using-machine-learning.md)
* [Сохраненная процедура деятельности](transform-data-using-stored-procedure.md)
