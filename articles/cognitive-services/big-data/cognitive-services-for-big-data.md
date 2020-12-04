---
title: Cognitive Services для больших данных
description: Узнайте, как использовать Azure Cognitive Services для обработки больших наборов данных с помощью Python, Java и Scala. Кроме того, вы можете внедрять постоянно улучшающиеся интеллектуальные модели непосредственно в Apache Spark&trade; и вычисления SQL.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: aaade03edbbb109656fb7371a063cdc2512c5a20
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461623"
---
# <a name="azure-cognitive-services-for-big-data"></a>Azure Cognitive Services для больших данных

![Azure Cognitive Services для больших данных](media/cognitive-services-big-data-overview.svg)

Службы Azure Cognitive Services для больших данных позволяют пользователям передавать терабайты данных через Cognitive Services с помощью [Apache Spark&trade;](/dotnet/spark/what-is-spark). С помощью Cognitive Services для больших данных вы с легкостью можете создавать крупномасштабные интеллектуальные приложения с любым хранилищем данных.

Кроме того, вы можете внедрять постоянно улучшающиеся интеллектуальные модели непосредственно в Apache Spark&trade; и вычисления SQL. Эти инструменты избавляют разработчиков от необходимости вникать в сетевые конфигурации, чтобы они могли сосредоточиться на создании интеллектуальных распределенных приложений.

## <a name="features-and-benefits"></a>Возможности и преимущества:

Cognitive Services для больших данных могут использовать службы из любого региона, а также [контейнерные службы Cognitive Services](../cognitive-services-container-support.md). Контейнеры поддерживают развертывания с высокой степенью автономности (вплоть до отсутствия подключения) и имеют сверхнизкую задержку ответа. Контейнерные службы Cognitive Services можно выполнять локально, непосредственно на рабочих узлах в кластере Spark или во внешнем оркестраторе, например Kubernetes.

## <a name="supported-services"></a>Поддерживаемые службы

[Службы Cognitive Services](../index.yml), доступные через API и пакеты SDK, помогают создавать интеллектуальные приложения разработчикам без опыта работы со средствами искусственного интеллекта или обработки и анализа данных. С помощью Cognitive Services вы можете научить свои приложения видеть, слышать, говорить, понимать и аргументировать. Для использования Cognitive Services ваше приложение должно отправлять данные службе по сети. После получения данных служба возвращает интеллектуальный ответ. Для рабочих нагрузок больших данных доступны следующие службы:

### <a name="vision"></a>Зрение

|Имя службы|Описание службы|
|:-----------|:------------------|
|[Компьютерное зрение](../computer-vision/index.yml "API Компьютерного зрения")| Служба "Компьютерное зрение" предоставляет доступ к передовым алгоритмам обработки изображений и возврата данных. |
|[Распознавание лиц](../face/index.yml "Распознавание лиц")| Служба "Распознавание лиц" обеспечивает доступ к расширенным алгоритмам, позволяя определять и распознавать лица на основе атрибутов. |

### <a name="speech"></a>Речь

|Имя службы|Описание службы|
|:-----------|:------------------|
|[Служба распознавания речи](../speech-service/index.yml "Служба "Речь"")|Служба "Речь" предоставляет доступ к таким функциям, как распознавание, синтез и перевод речи, а также проверка и идентификация говорящего.|

### <a name="decision"></a>Решение

|Имя службы|Описание службы|
|:-----------|:------------------|
|[Детектор аномалий](../anomaly-detector/index.yml "Детектор аномалий") | Служба "Детектор аномалий" (предварительная версия) позволяет отслеживать и обнаруживать отклонения в данных временных рядов.|

### <a name="language"></a>Язык

|Имя службы|Описание службы|
|:-----------|:------------------|
|[Анализ текста](../text-analytics/index.yml "Анализ текста")| Служба "Анализ текста" выполняет обработку естественного языка в виде необработанного текста для анализа тональности, извлечения ключевых фраз и определения языка.|

### <a name="search"></a>Поиск

|Имя службы|Описание службы|
|:-----------|:------------------|
|[Поиск изображений Bing](/azure/cognitive-services/bing-image-search "API Поиска изображений Bing")|Служба "Поиск изображений Bing" возвращает изображения, которые определены как соответствующие запросу пользователя.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>Поддерживаемые языки программирования для Cognitive Services для больших данных

Службы Cognitive Services для больших данных созданы на основе Apache Spark. Apache Spark — это библиотека распределенных вычислений, которая поддерживает Java, Scala, Python, R и многие другие языки. Сейчас эти языки поддерживаются.

### <a name="python"></a>Python

Мы предоставляем API PySpark в пространстве имен `mmlspark.cognitive` [Microsoft ML for Apache Spark](https://aka.ms/spark). Дополнительные сведения см. в [документации по API для разработчиков Python](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html). См. [примеры для Python](samples-python.md).

### <a name="scala-and-java"></a>Scala и Java

Мы предоставляем API Spark на основе Scala и Java в пространстве имен `com.microsoft.ml.spark.cognitive` [Microsoft ML for Apache Spark](https://aka.ms/spark). Дополнительные сведения см. в [документации по API для разработчиков Scala](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package). См. [примеры для Scala](samples-scala.md).

## <a name="supported-platforms-and-connectors"></a>Поддерживаемые платформы и соединители

Службы Cognitive Services для больших данных требуют наличия Apache Spark. Существует несколько платформ Apache Spark, которые поддерживают Cognitive Services для больших данных.

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) — это платформа аналитики на основе Apache Spark, оптимизированная для платформы облачных служб Microsoft Azure. Она предлагает специалистам в области обработки и анализа данных, инжиниринга данных и бизнес-аналитики интерактивную рабочую область с возможностями совместной работы, предоставляя возможности быстрой настройки и оптимизированные рабочие процессы.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Azure Synapse Analytics](/azure/databricks/data/data-sources/azure/synapse-analytics) — это корпоративное хранилище данных, которое использует средства параллельной обработки. С помощью Synapse Analytics можно быстро выполнять сложные запросы к петабайтам данных. Azure Synapse Analytics предоставляет управляемые пулы Spark для выполнения заданий Spark через интуитивно простой интерфейс Jupyter Notebook.

### <a name="azure-kubernetes-service"></a>Служба Azure Kubernetes

[Служба Azure Kubernetes (AKS)](../../aks/index.yml) управляет контейнерами Docker и распределенными приложениями в огромном масштабе. AKS — это управляемое предложение Kubernetes, которое упрощает использование Kubernetes в Azure. Kubernetes может обеспечить детальный контроль над масштабированием, задержкой и сетевой нагрузкой Cognitive Service. Но мы рекомендуем использовать Azure Databricks или Azure Synapse Analytics, если у вас нет опыта работы с Apache Spark.

### <a name="data-connectors"></a>Соединители данных

После развертывания кластера Spark вам нужно подключиться к своим данным. Apache Spark предлагает множество соединителей баз данных. Такие соединители позволяют приложениям работать с крупными наборами данных независимо от места их хранения. Дополнительные сведения о поддерживаемых базах данных и соединителях см. в [списке поддерживаемых источников данных для Azure Databricks](/azure/databricks/data/data-sources/).

## <a name="concepts"></a>Основные понятия

### <a name="spark"></a>Spark

[Apache Spark&trade;](http://spark.apache.org/) — это единый аналитический механизм для крупномасштабной обработки данных. Его платформа параллельной обработки увеличивает производительность приложений больших данных и аналитики. Spark может работать как система пакетной или потоковой обработки без изменения основного кода приложения.

В основе Spark лежит DataFrame — табличная коллекция данных, распределенных между рабочими узлами Apache Spark. Spark DataFrame соответствует таблице в реляционной базе данных или кадру данных в R/Python, но с неограниченным масштабом. Коллекции DataFrame можно собирать из разных источников, например из файлов структурированных данных, таблиц в Hive или внешних баз данных. С данными в Spark DataFrame вы можете:

   - выполнять вычисления в стиле SQL, например объединение и фильтрацию таблиц;
   - применять функции к крупным наборам данных с использованием параллелизма в стиле MapReduce;
   - применять возможности распределенного машинного обучения с использованием Microsoft Machine Learning for Apache Spark;
   - использовать Cognitive Services для больших данных для обогащения данных с помощью готовых к использованию интеллектуальных служб;

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>Microsoft Machine Learning for Apache Spark (MMLSpark)

[Microsoft Machine Learning for Apache Spark](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) — это библиотека распределенного машинного обучения с открытым кодом на основе Apache Spark. Службы Cognitive Services для больших данных входят в состав этого пакета. Кроме того, MMLSpark содержит другие инструменты машинного обучения для Apache Spark, например LightGBM, Vowpal Wabbit, OpenCV, LIME и др. С помощью MMLSpark вы можете создавать мощные прогнозные и аналитические модели на основе любого источника данных Spark.

### <a name="http-on-spark"></a>HTTP в Spark

Cognitive Services для больших данных — это пример того, как мы интегрируем интеллектуальные веб-службы с большими данными. Веб-службы обеспечивают поддержку множества приложений по всему миру. Большинство из этих служб передает данные по протоколу HTTP. Для работы с *произвольными* веб-службами в большом масштабе мы предоставляем HTTP в Spark. С помощью HTTP в Spark вы можете передавать терабайты данных через любую веб-службу. Именно эту технологию мы используем для работы Cognitive Services для больших данных.

## <a name="developer-samples"></a>Примеры для разработчиков

- [Рецепт. Прогнозное обслуживание](recipes/anomaly-detection.md)
- [Рецепт. Интеллектуальный анализ предметов искусства](recipes/art-explorer.md)

## <a name="blog-posts"></a>Записи блога

- [Подробные сведения о работе Cognitive Services в Apache Spark&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Спасение снежных барсов с помощью глубокого обучения и Компьютерного зрения в Spark](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Подкаст Microsoft Research, посвященный тому, как MMLSpark расширяет возможности AI for Good (Марк Хэмилтон (Mark Hamilton))](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)
- [Научная статья о крупномасштабных интеллектуальных микрослужбах](https://arxiv.org/abs/2009.08044)

## <a name="webinars-and-videos"></a>Вебинары и видео

- [Azure Cognitive Services в Spark — кластеры с внедренными интеллектуальными службами](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Основные тезисы конференции Spark Summit — масштабируемый ИИ во благо: программа AI for Good](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Cognitive Services для больших данных в Cosmos DB](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)
- [Блиц-доклад о крупномасштабных интеллектуальных микрослужбах](https://www.youtube.com/watch?v=BtuhmdIy9Fk&t=6s)

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы с Cognitive Services для больших данных](getting-started.md)
- [Простые примеры для Python](samples-python.md)
- [Простые примеры для Scala](samples-scala.md)