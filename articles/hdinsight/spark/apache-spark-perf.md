---
title: Оптимизация заданий Spark для повышения производительности в Azure HDInsight
description: В этой статье описываются распространенные стратегии повышения производительности кластеров Apache Spark в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780104"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Оптимизация заданий Apache Spark в Azure HDInsight

В этой статье приводятся общие сведения о стратегиях оптимизации заданий Apache Spark в Azure HDInsight.

## <a name="overview"></a>Обзор

Производительность заданий Apache Spark зависит от нескольких факторов. К этим факторам относятся: способ хранения данных, способ настройки кластера и операции, используемые при обработке данных.

Наиболее распространенной проблемой, с которой можно столкнуться, является нехватка памяти. Причинами этой проблемы можно назвать неправильные размеры исполнителей, длительные операции и задачи, которые приводят к декартовым операциям.

Существуют также различные стратегии, которые могут помочь решить такие трудности, как кэширование, и устранить неравномерное распределение данных.

В каждой из следующих статей представлены сведения о распространенных проблемах и решения для различных аспектов оптимизации Spark.

* [Оптимизация хранения данных](optimize-data-storage.md)
* [Оптимизация обработки данных](optimize-data-processing.md)
* [Оптимизация использования памяти](optimize-memory-usage.md)
* [Оптимизация конфигурации кластера](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Отладка заданий Apache Spark в Azure HDInsight](apache-spark-job-debugging.md)
* [Управление ресурсами для кластера Apache Spark в HDInsight](apache-spark-resource-manager.md)
* [Настройка параметров Apache Spark](apache-spark-settings.md)
