---
title: Оптимизация заданий Spark для повышения производительности в Azure HDInsight
description: В этой статье описываются распространенные стратегии повышения производительности кластеров Apache Spark в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 0b152f7882c7d7a3bab762253da0febc0257ceae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84117962"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Оптимизация заданий Apache Spark в Azure HDInsight

В этой статье приводятся общие сведения о стратегиях оптимизации заданий Apache Spark в Azure HDInsight.

## <a name="overview"></a>Обзор

Производительность заданий Apache Spark зависит от нескольких факторов. К этим факторам относятся: способ хранения данных, способ настройки кластера и операции, используемые при обработке данных.

Распространенные проблемы, с которыми вы можете столкнуться: ограничения памяти из-за неправильного размера исполнителей, длительных операций и задач, которые привели к появлению операций Декарт.

Также существует множество оптимизаций, которые могут помочь преодолеть такие трудности, как кэширование, и разрешить неравномерное распределение данных.

В каждой из следующих статей можно найти сведения о различных аспектах оптимизации Spark.

* [Оптимизация хранилища данных для Apache Spark](optimize-data-storage.md)
* [Оптимизация обработки данных для Apache Spark](optimize-data-processing.md)
* [Оптимизация потребления памяти для Apache Spark](optimize-memory-usage.md)
* [Оптимизация конфигурации кластера HDInsight для Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Отладка заданий Apache Spark в Azure HDInsight](apache-spark-job-debugging.md)
* [Управление ресурсами для кластера Apache Spark в HDInsight](apache-spark-resource-manager.md)
* [Настройка параметров Apache Spark](apache-spark-settings.md)
