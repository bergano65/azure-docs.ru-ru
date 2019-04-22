---
title: 'Azure HDInsight: Примеры для Java'
description: См. примеры Java на GitHub для выполнения распространенных задач с использованием пакета SDK HDInsight для Java.
author: hrasheed-msft
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.author: hrasheed
ms.openlocfilehash: 926f067a355dcc1503e6160001cf4c47a444e1b0
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59580944"
---
# <a name="azure-hdinsight-java-samples"></a>Azure HDInsight: Примеры для Java

> [!div class="op_single_selector"]
> * [Примеры Java](hdinsight-sdk-java-samples.md)
> * [Примеры .NET](hdinsight-sdk-dotnet-samples.md)
> * [Примеры Python](hdinsight-sdk-python-samples.md)
<!-- * [Go Examples](hdinsight-sdk-dotnet-samples.md)-->

Эта статья содержит:

* Ссылки на примеры для задач создания кластера.
* Ссылки на справочные материалы для других задач управления.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Пакет SDK Azure HDInsight для Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Управление кластерами: создание кластера

* [Создание кластера Kafka](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateKafkaClusterSample.java)
* [Создание кластера Spark](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateSparkClusterSample.java)
* [Создание кластера Spark с Azure Data Lake Storage 2-го поколения](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateHadoopClusterWithAdlsGen2Sample.java)
* [Создание кластера Spark с Корпоративным пакетом безопасности (ESP)](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateEspClusterSample.java)

Эти примеры для Java можно получить, клонировав репозиторий GitHub [hdinsight-java-sdk-samples](https://github.com/Azure-Samples/hdinsight-java-sdk-samples).

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Фрагменты кода для этой дополнительной функции пакета SDK см. в [справочной документации по пакету SDK HDInsight для Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-preview).