---
title: Cognitive Services для больших данных — примеры Python
description: Используйте примеры Cognitive Services в Python для Azure Databricks, чтобы запустить конвейер MMLSpark для больших данных.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: caf492c2cd3940fd7f37e2a4462c8376a127f393
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86189329"
---
# <a name="python-samples-for-cognitive-services-for-big-data"></a>Примеры Python для Cognitive Services для больших данных

Приведенные ниже фрагменты кода готовы к выполнению. Они помогут вам начать работу с Cognitive Services в Spark с использованием Python.

В примерах, приведенных в этой статье, используются следующие службы Cognitive Services:

- Анализ текста — получение сведений о тональности (или настроении) набора предложений;
- Компьютерное зрение — получение тегов (описание с использованием одного слова), связанных с набором изображений;
- Поиск изображений Bing — поиск в Интернете изображений, соответствующих запросу на естественном языке;
- Преобразование речи в текст — транскрибирование аудиофайлов для извлечения текстовых расшифровок;
- Детектор аномалий — обнаружение аномалий в данных временных рядов.

## <a name="prerequisites"></a>Предварительные требования

1. Чтобы настроить среду Cognitive Services и Azure Databricks, выполните действия, описанные в статье [Начало работы](getting-started.md). В этом руководстве показано, как установить MMLSpark и создать кластер Spark в Databricks.
1. Создав записную книжку в Azure Databricks, скопируйте **общий код** ниже и вставьте его в новую ячейку записной книжки.
1. Выберите пример службы ниже и скопируйте его во вторую новую ячейку записной книжки.
1. Замените все заполнители ключа подписки службы своим ключом.
1. Нажмите кнопку выполнения (значок с треугольником) в правом верхнем углу ячейки и щелкните **Выполнить ячейку**.
1. Просмотрите результаты в таблице под ячейкой.

## <a name="shared-code"></a>Общий код

Чтобы приступить к работе, необходимо добавить следующий код в проект.

```python
from mmlspark.cognitive import *

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key = "ADD_YOUR_SUBSCRIPION_KEY"
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY"
# An Anomaly Dectector subscription key
anomaly_key = "ADD_YOUR_SUBSCRIPION_KEY"

# Validate the key
assert service_key != "ADD_YOUR_SUBSCRIPION_KEY"
```    

## <a name="text-analytics-sample"></a>Пример для службы "Анализ текста"

Служба [Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) предоставляет несколько алгоритмов для извлечения интеллектуальной аналитики из текста. Например, можно определить тональность заданного входного текста. Служба вернет оценку между 0,0 и 1,0: низкий показатель указывает на отрицательную тональность, а высокий — на положительную.  В этом примере используются три простых предложения. Для каждого из них возвращается тональность.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format
display(sentiment.transform(df).select("text", col("sentiment")[0].getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>Ожидаемый результат

| text                                      | Тональность                                             |
|:------------------------------------------|:------------------------------------------------------|
| I am so happy today, its sunny!           | 0.9789592027664185                                    |
| I am frustrated by this rush hour traffic | 0.023795604705810547                                  |
| The cognitive services on spark aint bad  | 0.8888956308364868                                    |

## <a name="computer-vision-sample"></a>Пример для службы "Компьютерное зрение"

Служба [Компьютерное зрение](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) анализирует изображения для выявления таких структур, как лица, объекты и описания на естественном языке. В этом примере мы отметим тегами список изображений. Теги — это описания вещей, выраженные одним словом, например распознаваемых объектов, людей, пейзажей и действий.

```python

# Create a dataframe with the image URLs
df = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select("image", "analysis_results.description.tags"))
```

### <a name="expected-result"></a>Ожидаемый результат

| Изображение | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skating' 'person' 'man' 'outdoor' 'riding' 'sport' 'skateboard' 'young' 'board' 'shirt' 'air' 'black' 'park' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'fence' 'wooden' 'small' 'brown' 'building' 'sitting' 'front' 'bench' 'standing' 'table' 'walking' 'board' 'beach' 'white' 'holding' 'bridge' 'track']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'grass' 'house' 'building' 'old' 'home' 'front' 'small' 'church' 'stone' 'large' 'grazing' 'yard' 'green' 'sitting' 'leading' 'sheep' 'brick' 'bench' 'street' 'white' 'country' 'clock' 'sign' 'parked' 'field' 'standing' 'garden' 'water' 'red' 'horse' 'man' 'tall' 'fire' 'group']


## <a name="bing-image-search-sample"></a>Пример для службы "Поиск изображений Bing"

[Поиск изображений Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview) — поиск в Интернете изображений, соответствующих запросу пользователя на естественном языке. В этом примере мы используем текстовый запрос для поиска изображений с кавычками. Он возвращает список URL-адресов изображений, содержащих фотографии, связанные с нашим запросом.

```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 10
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(100)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bing_search_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")

# This displays the full results returned, uncomment to use
# display(bingSearch.transform(bingParameters))

# Since we have two services, they are put into a pipeline
pipeline = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
display(pipeline.transform(bingParameters))
```

### <a name="expected-result"></a>Ожидаемый результат

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |


## <a name="speech-to-text-sample"></a>Пример преобразования речи в текст
Служба [Преобразование речи в текст](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text) преобразует потоки или файлы с речью в текст. В этом примере мы транскрибируем два звуковых файла. Первый файл легко распознать, а второй — несколько сложнее.

```python

# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",),
                           ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```

### <a name="expected-result"></a>Ожидаемый результат

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Custom speech provides tools that allow you to visually inspect the recognition quality of a model by comparing audio data with the corresponding recognition result from the custom speech portal. You can playback uploaded audio and determine if the provided recognition result is correct. This tool allows you to quickly inspect quality of Microsoft's baseline speech to text model or a trained custom model without having to transcribe any audio data. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Add a gentleman Sir thinking visual check.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | I hear me. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | I like the reassurance for radio that I can hear it as well. |


## <a name="anomaly-detector-sample"></a>Пример для службы "Детектор аномалий"

[Детектор аномалий](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/) — удобное средство для обнаружения несоответствующих данных во временных рядах. В этом примере мы используем службу для поиска аномалий во всех временных рядах.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomaly_key)
  .setLocation("eastus")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>Ожидаемый результат

| TIMESTAMP            |   value | isAnomaly   |
|:---------------------|--------:|:------------|
| 1972-01-01T00:00:00Z |     826 | False       |
| 1972-02-01T00:00:00Z |     799 | False       |
| 1972-03-01T00:00:00Z |     890 | False       |
| 1972-04-01T00:00:00Z |     900 | False       |
| 1972-05-01T00:00:00Z |     766 | False       |
| 1972-06-01T00:00:00Z |     805 | False       |
| 1972-07-01T00:00:00Z |     821 | False       |
| 1972-08-01T00:00:00Z |   20 000 | True        |
| 1972-09-01T00:00:00Z |     883 | False       |
| 1972-10-01T00:00:00Z |     898 | False       |
| 1972-11-01T00:00:00Z |     957 | False       |
| 1972-12-01T00:00:00Z |     924 | False       |
| 1973-01-01T00:00:00Z |     881 | False       |
| 1973-02-01T00:00:00Z |     837 | False       |
| 1973-03-01T00:00:00Z |    9000 | True        |

## <a name="arbitrary-web-apis"></a>Произвольные веб-API

Благодаря поддержке HTTP в Spark вы можете использовать любую веб-службу в конвейере больших данных. В этом примере мы используем [API World Bank](http://api.worldbank.org/v2/country/) для получения сведений о разных странах мира.

```python
from requests import Request
from mmlspark.io.http import HTTPTransformer, http_udf
from pyspark.sql.functions import udf, col

# Use any requests from the python requests library
def world_bank_request(country):
  return Request("GET", "http://api.worldbank.org/v2/country/{}?format=json".format(country))

# Create a dataframe with spcificies which countries we want data on
df = (spark.createDataFrame([("br",),("usa",)], ["country"])
  .withColumn("request", http_udf(world_bank_request)(col("country"))))

# Much faster for big data because of the concurrency :)
client = (HTTPTransformer()
      .setConcurrency(3)
      .setInputCol("request")
      .setOutputCol("response"))

# Get the body of the response
def get_response_body(resp):
  return resp.entity.content.decode()

# Show the details of the country data returned
display(client.transform(df).select("country", udf(get_response_body)(col("response")).alias("response")))
```

### <a name="expected-result"></a>Ожидаемый результат

| country   | Ответ |
|:----------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| br | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"BRA","iso2Code":"BR","name":"Brazil","region":{"id":"LCN","iso2code":"ZJ","value":"Latin America & Caribbean "},"adminregion":{"id":"LAC","iso2code":"XJ","value":"Latin America & Caribbean (excluding high income)"},"incomeLevel":{"id":"UMC","iso2code":"XT","value":"Upper middle income"},"lendingType":{"id":"IBD","iso2code":"XF","value":"IBRD"},"capitalCity":"Brasilia","longitude":"-47.9292","latitude":"-15.7801"}]] |
| usa  | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"USA","iso2Code":"US","name":"United States","region":{"id":"NAC","iso2code":"XU","value":"North America"},"adminregion":{"id":"","iso2code":"","value":""},"incomeLevel":{"id":"HIC","iso2code":"XD","value":"High income"},"lendingType":{"id":"LNX","iso2code":"XX","value":"Not classified"},"capitalCity":"Washington D.C.","longitude":"-77.032","latitude":"38.8895"}]] |

## <a name="see-also"></a>См. также раздел

* [Рецепт. Обнаружение аномалий](./recipes/anomaly-detection.md)
* [Рецепт. Анализ предметов искусства](./recipes/art-explorer.md)
