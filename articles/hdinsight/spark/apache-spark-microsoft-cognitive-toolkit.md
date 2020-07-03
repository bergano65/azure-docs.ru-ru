---
title: Когнитивный инструмент Microsoft с Apache Spark - Azure HDInsight
description: Узнайте, как можно применить обученную модель обучения Microsoft Cognitive Toolkit к набору данных с помощью API Python для Spark в кластере Azure HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 1933db624dfef2ffa747ecb043be6730b6b884b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206560"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Использование модели глубокого обучения Microsoft Cognitive Toolkit в кластере Azure HDInsight Spark

Ниже перечислены действия, которые вы выполните в этой статье.

1. Запустите пользовательский скрипт для установки [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) в кластер Azure HDInsight Spark.

2. передача [Jupyter Notebook](https://jupyter.org/) в кластер [Apache Spark](https://spark.apache.org/) для применения обученной модели глубокого обучения Microsoft Cognitive Toolkit к файлам в учетной записи хранилища BLOB-объектов Azure с помощью [API Python для Spark (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html).

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Spark в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Создание кластера Apache Spark в HDInsight с помощью шаблона](./apache-spark-jupyter-spark-sql-use-portal.md).

* Опыт работы с записными книжками Jupyter с Spark в HDInsight. Дополнительные сведения см. в статье [Руководство. Загрузка данных и выполнение запросов в кластере Apache Spark в Azure HDInsight](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Как реализуется это решение?

Это решение разделено между этой статьей и ноутбуком Jupyter, который вы загружаете в рамках этой статьи. Ниже перечислены действия, которые вы выполните в этой статье.

* Запуск действия сценария в кластере HDInsight Spark для установки Microsoft Cognitive Toolkit и пакетов Python.
* Передача элемента Jupyter Notebook, запускающего решение в кластере HDInsight Spark.

Перечисленные ниже оставшиеся шаги приведены в описании Jupyter Notebook.

* Загрузите образец изображений в набор распределенных данных Spark Resilient или RDD.
  * Загрузка модулей и определение предустановок.
  * Скачивание набора данных локально в кластер Spark.
  * Преобразование набора данных в RDD.
* Оценка изображений с помощью обученной модели Cognitive Toolkit.
  * Скачивание обученной модели Cognitive Toolkit в кластер Spark.
  * Определение функций, используемых рабочими узлами.
  * Оценка изображений на рабочих узлах.
  * Анализ точности модели.

## <a name="install-microsoft-cognitive-toolkit"></a>Установка Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit в кластере Spark можно установить с помощью действия сценария. Действие скрипта использует пользовательские скрипты для установки компонентов в кластер, которые недоступны по умолчанию. Пользовательский скрипт с портала Azure можно использовать с помощью HDInsight .NET SDK или с помощью Azure PowerShell. Этот сценарий можно также использовать для установки данного набора средств при создании кластера или после его подготовки и запуска.

В этой статье мы используем портал для установки набора средств после того, как кластер был создан. Другие способы выполнения пользовательского сценария описаны в разделе [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Использование портала Azure

Для получения инструкций о том, как использовать портал [Customize HDInsight clusters using Script Action](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)Azure для выполнения действия скрипта, см. Обязательно укажите приведенные ниже данные для установки Microsoft Cognitive Toolkit. Используйте следующие значения для действия скрипта:

|Свойство |Значение |
|---|---|
|Тип скрипта|- Custom|
|Имя| Установка MCT|
|URI bash-скрипта|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Типы узлов:|Голова, Рабочий|
|Параметры|None|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Передача Jupyter Notebook в кластер Azure HDInsight Spark

Чтобы использовать Microsoft Cognitive Toolkit с кластером Azure HDInsight Spark, необходимо загрузить Jupyter Notebook **CNTK_model_scoring_on_Spark_walkthrough.ipynb** в кластер Azure HDInsight Spark. Этот ноутбук доступен на [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)GitHub на .

1. Скачать и [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)распаковать .

1. Из веб-браузера, `https://CLUSTERNAME.azurehdinsight.net/jupyter`перейдите к , где `CLUSTERNAME` имя вашего кластера.

1. Из ноутбука Jupyter выберите **Загрузка** в правом верхнем углу, `CNTK_model_scoring_on_Spark_walkthrough.ipynb`а затем перейдите к загрузке и выберите файл.

    ![Загрузите ноутбук Jupyter в кластер Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Загрузите ноутбук Jupyter в кластер Azure HDInsight Spark")

1. Выберите **Загрузить** еще раз.

1. После загрузки ноутбука щелкните название ноутбука, а затем следуйте инструкциям в самом блокноте о том, как загрузить набор данных и выполнить статью.

## <a name="see-also"></a>См. также раздел

* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии

* [Использование Apache Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Apache Spark и Машинное обучение. Анализ температуры в здании на основе данных системы кондиционирования с помощью Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark и Машинное обучение. Прогнозирование результатов проверки пищевых продуктов с помощью Spark в HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журналов веб-сайтов с помощью Apache Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Анализ журналов телеметрии Application Insights с помощью Apache Spark в HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений

* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Apache Livy в кластере Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения

* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Apache Spark с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра для записной книжки Jupyter в кластерах Apache Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
