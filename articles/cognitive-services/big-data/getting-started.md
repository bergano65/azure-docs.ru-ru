---
title: Начало работы с Cognitive Services для больших данных
description: Настройте конвейер MMLSpark с помощью Cognitive Services в Azure Databricks и выполните пример.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 095f2c3ed17042bb616fb091d1af52a64c913709
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460499"
---
# <a name="getting-started"></a>Начало работы

Настройка среды — это первый шаг к созданию конвейера для данных. Когда ваша среда будет готова, вы сможете быстро и без усилий выполнить пример.

В этой статье показано, как выполнить следующие шаги, чтобы начать работу:

1. [Создание ресурса Cognitive Services](#create-a-cognitive-services-resource)
1. [Создание кластера Apache Spark](#create-an-apache-spark-cluster)
1. [Выполнение приложения](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Создание ресурса Cognitive Services

Чтобы использовать Cognitive Services для больших данных, необходимо сначала создать экземпляр этих служб для нашего рабочего процесса. Службы Cognitive Services бывают двух типов: облачные службы, размещенные в Azure, и контейнерные службы, управляемые пользователями. Мы рекомендуем начать с более простого варианта на основе облака.

### <a name="cloud-services"></a>Облачные службы

Облачные службы Cognitive Services — это интеллектуальные алгоритмы, размещаемые в Azure. Эти службы готовы к использованию без обучения, требуется только подключение к Интернету. [Службы Cognitive Services можно создать на портале Azure](../cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows) или с помощью [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows).

### <a name="containerized-services-optional"></a>Контейнерные службы (необязательно)

Если приложение или рабочая нагрузка использует крупные наборы данных, требует использовать частную сеть или не может получить доступ к облаку, обмен данными с облачными службами может оказаться невозможным. В этом случае контейнерные Cognitive Services имеют следующие преимущества:

* **Высокая степень автономности.** Контейнерные службы Cognitive Services можно развернуть в любой вычислительной среде как в облаке, так и в автономном режиме. Если приложение не может получить доступ к облаку, попробуйте развернуть контейнерные службы Cognitive Services в приложении.

* **Минимальная задержка.** Так как контейнерным службам не нужно обмениваться данными с облаком, ответы возвращаются с более низкими задержками.

* **Конфиденциальность и защита данных.** Контейнерные службы можно развернуть в частных сетях, чтобы конфиденциальные данные не выходили за их пределы.

* **Высокий уровень масштабируемости.** Контейнерные службы не имеют ограничений скорости и выполняются на компьютерах, управляемых пользователем. Таким образом, вы можете свободно масштабировать Cognitive Services, чтобы обрабатывать очень большие рабочие нагрузки.

См. [это руководство](../cognitive-services-container-support.md?tabs=luis), чтобы создать контейнерный экземпляр Cognitive Services.

## <a name="create-an-apache-spark-cluster"></a>Создание кластера Apache Spark

[Apache Spark&trade;](http://spark.apache.org/) — это платформа распределенных вычислений, предназначенная для обработки больших данных. Пользователи могут работать с Apache Spark в Azure с такими службами, как Azure Databricks, Azure Synapse Analytics, HDInsight и Служба Azure Kubernetes. Чтобы использовать Cognitive Services для больших данных, необходимо сначала создать кластер. Если у вас уже есть кластер Spark, переходите к работе с примером.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks — это платформа для аналитики на базе Apache Spark. Она настраивается одним щелчком, упрощает рабочие процессы и предоставляет интерактивную рабочую область, а также часто используется для совместной работы инженеров, бизнес-аналитиков и специалистов по обработке и анализу данных. Чтобы использовать Cognitive Services для больших данных в Azure Databricks, сделайте следующее:

1. [Создайте рабочую область Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace).
1. [Создание кластера Spark в Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. Установка Cognitive Services для больших данных
    * Создайте библиотеку в рабочей области Databricks.  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * Введите следующие координаты Maven:  `com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3`. Репозиторий: `https://mmlspark.azureedge.net/maven`.  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * Установите библиотеку в кластере.  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="azure-synapse-analytics-optional"></a>Azure Synapse Analytics (необязательно)

При необходимости для создания кластера Spark можно использовать Synapse Analytics. Azure Synapse Analytics объединяет корпоративные хранилища данных и аналитику больших данных. Эта служба позволяет вам запрашивать данные в соответствии с вашими требованиями, используя бессерверные ресурсы по запросу или подготовленные ресурсы в большом масштабе. Чтобы начать работу с Azure Synapse Analytics, сделайте следующее:

1. [Создайте рабочую область Synapse (предварительная версия).](../../synapse-analytics/quickstart-create-workspace.md)
1. [Создание бессерверного пула Apache Spark (предварительная версия) с помощью портала Azure.](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

В Azure Synapse Analytics компонент больших данных для Cognitive Services устанавливается по умолчанию.

### <a name="azure-kubernetes-service"></a>Служба Azure Kubernetes

Если вы используете контейнерные службы Cognitive Services, один из популярных вариантов развертывания Spark вместе с контейнерами — использовать Службу Azure Kubernetes.

Чтобы начать работу со Службой Azure Kubernetes, сделайте следующее:

1. [Развертывание кластера Службы Azure Kubernetes (AKS) с помощью портала Azure](../../aks/kubernetes-walkthrough-portal.md)
1. [Установите чарт Helm Apache Spark 2.4.0.](https://hub.helm.sh/charts/microsoft/spark)
1. [Установите контейнер Cognitive Services с помощью Helm.](../computer-vision/deploy-computer-vision-on-premises.md)

## <a name="try-a-sample"></a>Выполнение примера

Настроив кластер Spark и среду, можно выполнить короткий пример. В этом разделе показано, как использовать компоненты больших данных для Cognitive Services в Azure Databricks.

Сначала можно создать записную книжку в Azure Databricks. Если вы работаете с другими поставщиками кластеров Spark, используйте соответствующие записные книжки или Spark Submit.

1. Создайте записную книжку Databricks, выбрав **New notebook** (Создать записную книжку) в меню **Azure Databricks**.

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. В диалоговом окне **создания записной книжки** введите имя, выберите **Python** в качестве языка, а затем выберите созданный ранее кластер Spark.

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    Нажмите кнопку **создания**.

1. Вставьте этот фрагмент кода в новую записную книжку.

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Получите ключ подписки в меню **Keys and Endpoint** (Ключи и конечная точка) на панели мониторинга службы "Анализ текста" на портале Azure.
1. Замените заполнитель ключа подписки в коде записной книжки Databricks своим ключом подписки.
1. Щелкните значок воспроизведения или треугольника в правом верхнем углу ячейки записной книжки, чтобы выполнить пример. При необходимости щелкните **Выполнить все** в верхней части записной книжки, чтобы выполнить все ячейки. Ответы отобразятся под ячейкой в таблице.

### <a name="expected-results"></a>Ожидаемые результаты

| text                                      |   Тональность |
|:------------------------------------------|------------:|
| I am so happy today, its sunny!           |   0.978959  |
| I am frustrated by this rush hour traffic |   0.0237956 |
| The cognitive services on spark aint bad  |   0.888896  |

## <a name="next-steps"></a>Дальнейшие действия

- [Краткие примеры Python](samples-python.md)
- [Краткие примеры Scala](samples-scala.md)
- [Рецепт. Прогнозное обслуживание](recipes/anomaly-detection.md)
- [Рецепт. Интеллектуальный анализ предметов искусства](recipes/art-explorer.md)