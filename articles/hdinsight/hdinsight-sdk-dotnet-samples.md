---
title: Azure HDInsight. Примеры .NET
description: См. примеры C# .NET на GitHub для выполнения распространенных задач с использованием пакета SDK HDInsight для .NET.
author: hrasheed-msft
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.author: hrasheed
ms.openlocfilehash: f589c5e23be105086d2908fb7db72e69c781e726
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678264"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight. Примеры .NET

> [!div class="op_single_selector"]
> * [Примеры .NET](hdinsight-sdk-dotnet-samples.md)
> * [Примеры Python](hdinsight-sdk-python-samples.md)
> * [Примеры Java](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

Эта статья содержит:

* Ссылки на примеры для задач создания кластера.
* Ссылки на справочные материалы для других задач управления.

Вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): в вашей подписке Visual Studio каждый месяц зачисляются деньги на счет, которые можно использовать для оплаты служб Azure.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Пакет SDK Azure HDInsight для .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Управление кластерами: создание кластера

* [Создание кластера Kafka](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Создание кластера Spark](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Создание кластера Spark с Azure Data Lake Storage 2-го поколения](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [Создание кластера Spark с Корпоративным пакетом безопасности (ESP)](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

Эти примеры для .NET можно получить, клонировав репозиторий GitHub [hdinsight-dotnet-sdk-samples](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples).

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Фрагменты кода для этой дополнительной функции пакета SDK см. в [справочной документации по пакету SDK HDInsight для .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).