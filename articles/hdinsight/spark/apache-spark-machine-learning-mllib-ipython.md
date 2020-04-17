---
title: Пример машинного обучения с использованием Spark MLlib в HDInsight — Azure
description: Узнайте, как использовать Spark MLlib для создания приложения машинного обучения, которое анализирует набор данных с помощью классификации посредством логистической регрессии.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: 26695df299ba5d0f50c8f271b5da99284a8d6764
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531139"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Использование Apache Spark MLlib для создания приложения машинного обучения и анализа набора данных

Узнайте, как использовать Apache Spark [MLlib](https://spark.apache.org/mllib/) для создания приложения для машинного обучения. Приложение будет делать прогностический анализ на открытом наборе данных. В этом примере используется *классификация* посредством логистической регрессии на основе встроенных библиотек машинного обучения Spark.

MLlib является основной библиотекой Spark, которая предоставляет множество утилит, полезных для задач машинного обучения, таких как:

* Классификация
* Регрессия
* Кластеризация
* Моделирование
* сингулярного разложения и анализа по методу главных компонент;
* проверки гипотез и статистической выборки.

## <a name="understand-classification-and-logistic-regression"></a>Общие сведения о классификации и логистической регрессии

*Классификация* — это распространенная задача машинного обучения, которая представляет собой процесс сортировки входных данных по категориям. Это задача алгоритма классификации, чтобы выяснить, как назначить "метки" для ввода данных, которые вы предоставляете. Например, можно подумать об алгоритме машинного обучения, который принимает информацию о запасах в качестве ввода. Затем делит акции на две категории: акции, которые вы должны продать и акции, которые вы должны держать.

Логистическая регрессия — один из алгоритмов классификации. API Spark для логистической регрессии подходит для задач *двоичной классификации* или разделения входных данных на две группы. Дополнительные сведения о логистической регрессии см. в статье [Википедии](https://en.wikipedia.org/wiki/Logistic_regression).

Таким образом, процесс логистической регрессии создает *логистическую функцию.* Используйте функцию для прогнозирования вероятности того, что вектор ввода относится к той или иной группе.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Пример прогнозного анализа на основе данных контроля качества пищевых продуктов

В этом примере вы используете Spark для проведения прогностического анализа данных инспекции пищевых продуктов **(Food_Inspections1.csv).** Данные, полученные через [портал данных города Чикаго](https://data.cityofchicago.org/). Этот набор данных содержит информацию о проверках предприятий пищевой промышленности, которые были проведены в Чикаго. Включая информацию о каждом учреждении, выявленные нарушения (если таковые имеется) и результаты проверки. CSV-файл данных уже доступен в учетной записи хранения, связанной с кластером **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

В следующих разделах описаны шаги по разработке модели, которая позволит узнать, что нужно, чтобы пройти контроль качества пищевых продуктов.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Создание приложения машинного обучения Apache Spark MLlib

1. Создайте записную книжку Jupyter, используя ядро PySpark. Инструкции см. в разделе по [созданию записной книжки Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Импортируйте типы, необходимые для этого приложения. Копировать и вставить следующий код в пустую ячейку, а затем нажмите **SHIFT и ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```

    Из-за ядра PySpark не нужно создавать какие-либо контексты явно. Контексты Spark и Hive автоматически создаются при запуске первой ячейки кода.

## <a name="construct-the-input-dataframe"></a>Создание входного кадра данных

Используйте контекст Spark, чтобы вытащить необработанные данные CSV в память в качестве неструктурированного текста. Затем используйте библиотеку CSV Python для анализа каждой строки данных.

1. Запустите следующие строки, чтобы импортировать входные данные и создать на их основе устойчивый распределенный набор данных (RDD).

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value

    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Выполните следующий код, чтобы получить из набора RDD одну строку, которая позволит изучить схему данных.

    ```PySpark
    inspections.take(1)
    ```

    Результаты:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    Эти выходные данные позволяют составить представление о схеме данных во входном файле. Она включает в себя название каждого учреждения, и тип учреждения. Кроме того, адрес, данные инспекций, и местоположение, среди прочего.

3. Выполните следующий код, чтобы создать кадр данных под (*df*) и временную таблицу (*CountResults*) с несколькими столбцами, которые помогут выполнить прогностический анализ. `sqlContext`используется для преобразований на структурированных данных.

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])

    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Четыре столбца, представляющие интерес для кадра данных, являются **ID,** **имя,** **результаты**и **нарушения.**

4. Выполните следующий код, чтобы получить небольшую выборку данных.

    ```PySpark
    df.show(5)
    ```

    Результаты:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Используемые данные

Давайте подробно рассмотрим содержимое нашего набора данных. 

1. Выполните следующий код, чтобы увидеть конкретные значения из столбца **результаты**:

    ```PySpark
    df.select('results').distinct().show()
    ```

    Результаты:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Выполните следующий код, чтобы создать визуализацию на основе этих результатов.

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    Волшебное слово `%%sql`, за которым следует `-o countResultsdf`, гарантирует, что вывод запроса сохраняется локально на сервере Jupyter (обычно это головной узел кластера). Выходные данные сохраняются в кадре данных [Pandas](https://pandas.pydata.org/) с именем **countResultsdf**. Дополнительные сведения о команде magic `%%sql`, а также других командах magic, доступных в ядре PySpark, приведены в статье [Ядра для записной книжки Jupyter в кластерах Apache Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Результаты:

    ![Результат SQL-запроса](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "Результат SQL-запроса")

3. Вы также можете использовать [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), библиотека, используемая для построения визуализации данных, для создания участка. Так как диаграмма должна создаваться из локально сохраненного кадра данных **countResultsdf**, фрагмент кода должен начинаться с волшебного слова `%%local`. Это действие гарантирует, что код будет работать локально на сервере Jupyter.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Чтобы спрогнозировать результат проверки пищевых продуктов, вам нужно разработать модель для анализа нарушений. Логистическая регрессия является методом двоичной классификации, а значит данные целесообразно разделить на две категории: **Fail** и **Pass**.

   - Успех
       - Успех
       - Pass w/ conditions;
   - Ошибка
       - Ошибка
   - Игнорировать
       - Business not located;
       - Out of Business.

     Данные с другими результатами ("Бизнес не расположен" или "Из бизнеса") не являются полезными, и они составляют небольшой процент от результатов в любом случае.

4. Выполните следующий код, чтобы преобразовать существующий кадр данных (`df`) в новый кадр данных, где каждая проверка будет представлена в виде пары "метка — нарушение". В этом случае метка `0.0` представляет собой сбой, метка `1.0` представляет `-1.0` успех, а метка представляет некоторые результаты, кроме этих двух результатов.

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Выполните следующий код, чтобы отобразить одну строку данных с меткой:

    ```PySpark
    labeledData.take(1)
    ```

    Результаты:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Создание модели логистической регрессии на основе входной таблицы данных

Окончательная задача заключается в преобразовании помеченных данных. Преобразуйте данные в формат, который может быть проанализирован с помощью логистической регрессии. Вход в алгоритм логистической регрессии нуждается в наборе *векторных пар меток-функция.* Где "вектором функций" является вектор чисел, представляющих точку ввода. Таким образом, необходимо преобразовать столбец «нарушения», который является полуструктурированным и содержит много комментариев в свободном тексте. Преобразуйте столбец в массив реальных чисел, которые машина может легко понять.

Один из стандартных подходов к машинному обучению для обработки естественного языка заключается в присвоении каждому отдельному слову "индекс". Затем передайте вектор алгоритму машинного обучения. Таким образом, что значение каждого индекса содержит относительную частоту этого слова в строке текста.

MLlib предоставляет простой способ сделать эту операцию. Сначала пометим каждую строку нарушений с помощью маркеров, чтобы определить отдельные слова в каждой строке. Затем используем `HashingTF`, чтобы преобразовать каждый набор маркеров в вектор признаков, который затем может быть передан в алгоритм логистической регрессии для создания модели. Для последовательного выполнения всех этих действий воспользуемся оператором pipeline.

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Оценка модели по другому набору данных

Модель, созданную ранее, можно использовать для *прогнозирования* результатов новых проверок. Прогнозы основаны на нарушениях, которые были замечены. Мы обучили эту модель по набору данных **Food_Inspections1.csv**. Вы можете применить другой набор данных, например **Food_Inspections2.csv**, чтобы *оценить* эффективность полученной модели на основе новых данных. Этот новый набор данных (**Food_Inspections2.csv**) следует поместить в контейнер хранилища по умолчанию, связанный с кластером.

1. Выполните приведенный ниже фрагмент кода, чтобы создать кадр данных **predictionsDf** с прогнозами нашей модели. Фрагмент кода также создает временную таблицу **Predictions** на основе кадра данных.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Вы должны увидеть вывод, как следующий текст:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Чтобы просмотреть пример прогноза, выполните этот фрагмент кода:

    ```PySpark
    predictionsDf.take(1)
    ```

   Есть прогноз для первой записи в наборе тестовых данных.

1. Метод `model.transform()` таким же образом преобразовывает новые данные и определяет, как их классифицировать. Вы можете сделать некоторые статистические данные, чтобы получить представление о том, как прогнозы были:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Выходные данные выглядят так:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Использование логистической регрессии с Spark дает вам модель взаимосвязи между описаниями нарушений на английском языке. И будет ли данный бизнес пройдет или не продовольственный осмотр.

## <a name="create-a-visual-representation-of-the-prediction"></a>Создание визуального представления прогноза

Теперь создайте итоговую визуализацию, которая позволит оценить результаты теста.

1. Начнем с извлечения разных прогнозов и результатов из временной таблицы **Predictions**, созданной ранее. Следующие запросы разделяют выходные данные на *true_positive*, *false_positive*, *true_negative* и *false_negative*. В приведенных ниже запросах мы отключим визуализацию с помощью `-q`, а также сохраним выходные данные (с помощью `-o`) как кадры данных, которые затем можно будет использовать с волшебным словом `%%local`.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Наконец, используйте следующий фрагмент кода для создания диаграммы с помощью **Matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Должны выводиться следующие данные:

    ![Выход приложения для машинного обучения Spark - процент ы неудавшихся проверок пищевых продуктов.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Результат результатов машинного обучения Spark")

    На этой диаграмме «положительный» результат представляет собой непройденную проверку, а отрицательный — пройденную.

## <a name="shut-down-the-notebook"></a>Завершение работы записной книжки

Завершив работу с приложением, следует закрыть записную книжку, чтобы освободить ресурсы. Для этого в меню **File** (Файл) записной книжки выберите пункт **Close and Halt** (Закрыть и остановить). Записная книжка завершит работу и закроется.

## <a name="next-steps"></a>Следующие шаги

* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии

* [Apache Spark с BI: Интерактивный анализ данных с использованием Spark в HDInsight с инструментами BI](apache-spark-use-bi-tools.md)
* [Apache Spark и Машинное обучение. Анализ температуры в здании на основе данных системы кондиционирования с помощью Spark в HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Анализ журналов веб-сайтов с помощью Apache Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

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
