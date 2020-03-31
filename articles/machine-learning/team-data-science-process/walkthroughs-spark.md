---
title: Аналитика в HDInsight Spark с использованием PySpark и Scala — Процесс обработки и анализа данных группы
description: Примеры процесса анализа данных команды, которые проходят через использование PySpark и Scala на Azure HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864151"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Пошаговое руководство обработки и анализа данных HDInsight Spark с использованием PySpark и Scala в Azure

В этих пошаговых руководствах для выполнения прогнозной аналитики в кластере Azure Spark используются PySpark и Scala. Они предусматривают выполнение инструкций, описанных в процессе обработки и анализа данных группы. Процесс обработки и анализа данных группы представлен в статье [Жизненный цикл процесса обработки и анализа данных группы](overview.md). Обзор Spark в HDInsight см. в статье [Общие сведения о Spark в HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Дополнительные пошаговые инструкции по обработке и анализу данных группы объединены по используемой **платформе**. Дополнительные сведения об этих примерах см. в статье [Пошаговые руководства по процессу обработки и анализа данных группы](walkthroughs.md).

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Прогнозирование чаевых за такси с помощью PySpark в Azure Spark

Используя данные о такси в Нью-йорке, [пошаговый обзор Use Spark на Azure HDInsight](spark-overview.md) предсказывает, оплачивается ли чаевые и диапазон ожидаемых сумм. В этом примере используется процесс исследования данных команды в сценарии с использованием [кластера Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) для хранения, изучения и оснащения инженерных данных из общедоступной поездки на такси и набора данных тарифов. В этой теме обзора используется кластер HDInsight Spark и ноутбуки Jupyter PySpark. В них рассказывается, как исследовать данные, а также как создавать и использовать модели. С помощью записной книжки по расширенному исследованию и моделированию данных можно более подробно ознакомиться с тем, как использовать перекрестную проверку, перебор гиперпараметров и оценку модели.

### <a name="data-exploration-and-modeling-with-spark"></a>Исследование и моделирование данных с помощью Spark 
Исследуйте набор данных, а затем создайте, оцените и проанализируйте модели машинного обучения, выполнив инструкции в статье [Исследование и моделирование данных с помощью Spark](spark-data-exploration-modeling.md).

### <a name="model-consumption"></a>Использование модели
Дополнительные сведения об оценке моделей классификации и регрессии, созданных в этой статье, см. в статье [Ввод моделей машинного обучения, созданных с помощью Spark, в эксплуатацию](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Перекрестная проверка и перебор гиперпараметров
О том, как модели могут быть обучены с помощью перекрестной проверки и гипер-параметра, [смотрите расширенное исследование и моделирование данных с помощью Spark.](spark-advanced-data-exploration-modeling.md)


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Прогнозирование чаевых за такси с помощью Scala в Azure Spark

[Использование Scala с Spark на Azure](scala-walkthrough.md) пошаговый предок прогнозирует ли чаевые выплачивается и диапазон сумм, как ожидается, будут выплачены. В этом руководстве показано, как с помощью Scala выполнять контролируемые задачи машинного обучения, используя библиотеку машинного обучения Spark (MLlib) и пакеты SparkML в кластере Azure HDInsight со Spark. Он просматривает задачи, составляющие [процесс data Science Process:](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)использование данных и исследование, визуализация, проектирование объектов, моделирование и потребление моделей. В моделях используются логистическая и линейная регрессия, случайные леса и градиентный бустинг деревьев.


## <a name="next-steps"></a>Дальнейшие действия

Обзор процесса подготовки данных team Data можно ознакомьтесь с [обзором процесса team Data Science Process.](overview.md)

Для обсуждения жизненного цикла процесса Team [Team Data Science Process lifecycle](lifecycle.md)Data Science Process см. Этот жизненный цикл описывает шаги, от начала до конца, которые проекты обычно следуют, когда они выполняются. 

