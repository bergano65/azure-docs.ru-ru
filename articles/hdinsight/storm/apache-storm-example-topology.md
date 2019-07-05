---
title: Примеры топологий Apache Storm в Azure HDInsight
description: Список примеров топологий Storm, созданных и протестированных с помощью Apache Storm в HDInsight, включая базовые топологии на C# и Java, а также работа с Центрами событий.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: a535510ce56147f708f1cd8219cf898e6e0df36b
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483787"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Примеры топологий и компонентов для Apache Storm в HDInsight

Ниже приведен список примеров для [Apache Storm](https://storm.apache.org/), разработанных и созданных корпорацией Майкрософт в HDInsight. Эти примеры охватывают целый ряд тем: от создания простейших топологий на C# и Java до использования таких служб Azure, как Центры событий, Cosmos DB, База данных SQL, [Apache HBase](https://hbase.apache.org/) в HDInsight и служба хранилища Azure. Некоторые примеры также демонстрируют использование средств, не относящихся к Azure, и даже технологий, не связанных с корпорацией Майкрософт, таких как SignalR и Socket.IO.

| Описание | Что демонстрирует | Язык или платформа |
|:--- |:--- |:--- |
| [Запись в Azure Data Lake Storage из Apache Storm](apache-storm-write-data-lake-store.md) |Запись в Azure Data Lake Storage |Java |
| [Источник воронки и сита концентратора событий](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Источник воронки и сита концентратора событий |Java |
| [Разработка топологий на платформе Java для Apache Storm в HDInsight][5797064f] |Maven |Java |
| [Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio][16fce2d1] |Средства HDInsight для Visual Studio |C#, Java |
| [Обработка событий из Центров событий Azure с помощью Apache Storm в HDInsight (C#)][844d1d81] |Центры событий; |C# и Java |
| [Обработка событий из службы "Центры событий" Azure с помощью Storm в HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Центры событий; |Java |
| [Обработка данных с датчиков автомобилей из Центров событий с помощью Apache Storm в HDInsight][246ee964] |Центры событий, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [Извлечения, преобразования и загрузки (ETL) из концентраторов событий Azure для Apache HBase, с помощью Apache Storm в HDInsight][b4b68194] |Центры событий, HBase |C# |
| [Шаблон C# проект топологии Storm для работы со службами Azure из Apache Storm в HDInsight][ce0c02a2] |Центры событий, Cosmos DB, база данных SQL, HBase, SignalR |C#, Java |
| [Тестирование масштабируемости для считывания из концентраторов событий Azure с использованием Apache Storm в HDInsight][d6c540e3] |Скорость обработки сообщений, Центры событий, база данных SQL |C#, Java |
| [Использование Python с Apache Storm в HDInsight](apache-storm-develop-python-topology.md) |Компоненты Python с топологией Flux |Python |
| [Использование Apache Kafka с Apache Storm в HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm: чтение и запись в Apache Kafka | Java |

> [!WARNING]  
> Примеры C# в этом списке были изначально созданы и протестированы с помощью HDInsight под управлением Windows и могут неправильно работать с кластерами HDInsight под управлением Linux. Кластеры под управлением Linux используют Mono для выполнения кода .NET. Могут возникнуть проблемы совместимости с платформами и пакетами, используемыми в примере.
>
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий.

### <a name="next-steps"></a>Следующие шаги

* [Создавать и отслеживать топологии Apache Storm в HDInsight Azure](./apache-storm-quickstart.md)
* [Узнайте, как развертывание и управление топологиями Apache Storm с помощью Apache Storm в HDInsight][6eb0d3b8]


[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Развертывание и администрирование топологий с помощью панели мониторинга Apache Storm и пользовательского интерфейса Storm или средств HDInsight для Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Разработка топологий Storm на языке Java с использованием Maven путем создания простой топологии для подсчета статистики."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Считывание данных из Центров событий Azure и запись их туда с использованием Storm в HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Обработка данных с датчиков автомобилей из Центров событий Azure с использованием средств Apache Storm в HDInsight."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Несколько топологий, демонстрирующих пропускную способность при считывании данных из Центров событий Azure и их записи в базу данных SQL с использованием средств Apache Storm в HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Узнайте, как считать данные из Центров событий Azure, а затем вычислить, преобразовать и сохранить эти данные в HBase в HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Этот проект содержит шаблоны воронок, сит и топологий, обеспечивающих взаимодействие с различными службами Azure, такими как Центры событий, Cosmos DB и база данных SQL."

