---
title: Руководство по Обнаружение аномалий при потоковой передаче данных с помощью Azure Databricks
titleSuffix: Azure Cognitive Services
description: Мониторинг аномалий в данных с помощью API Детектора аномалий и Azure Databricks.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: aahi
ms.openlocfilehash: 8d3f5d0e10fadd31fd8bde77339b872c1b90451f
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721472"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Руководство по Обнаружение аномалий при потоковой передаче данных с помощью Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) — это быстрая и удобная служба аналитики на базе Apache Spark с возможностью совместной работы. API Детектора аномалий является частью служб Azure Cognitive Services. Он предоставляет способ мониторинга данных временных рядов. В этом учебнике показано, как выполнить обнаружение аномалий при потоковой передаче данных практически в реальном времени с помощью Azure Databricks. Вы примете данные Twitter с помощью Центров событий Azure и импортируете их в Azure Databricks с помощью соединителя Центров событий Spark. После этого вы используете API для обнаружения аномалий в данных потоковой передачи. 

На следующем рисунке показан поток в приложении.

![Azure Databricks с Центрами событий и Cognitive Services](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks with Event Hubs and Cognitive Services")

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Создание рабочей области Azure Databricks
> * Создание кластера Spark в Azure Databricks.
> * Создание приложения Twitter для доступа к потоковым данных
> * Создание записных книжек в Azure Databricks.
> * Подключение библиотек к Центрам событий и API Twitter.
> * Создание ресурса "Детектор аномалий" и получение ключа доступа.
> * Отправка твитов в Центры событий.
> * Чтение твитов из Центров событий.
> * Выполнение обнаружения аномалий в твитах.

> [!Note]
> В этом учебнике представлен подход к реализации рекомендуемой [архитектуры решения](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) для API Детектора аномалий.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

> [!Note]
> Для выполнения действий в этом учебнике будет не достаточно ключа бесплатной пробной версии для API Детектора аномалий. Чтобы использовать бесплатную учетную запись для создания кластера Azure Databricks, перед созданием кластера перейдите в свой профиль и измените свою подписку на **оплату по мере использования**. Дополнительные сведения см. на странице [создания бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Предварительные требования

- [Пространство имен Центров событий Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) и концентратор событий.

- [Строка подключения](../../../event-hubs/event-hubs-get-connection-string.md) для получения доступа к пространству имен Центров событий. Строка подключения должна иметь формат:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- Имя политики общего доступа и ключ политики для Центров событий.

Дополнительные сведения о создании пространства имен и концентратора событий см. в статье [Краткое руководство. Создание концентратора событий с помощью портала Azure](../../../event-hubs/event-hubs-create.md).

## <a name="create-an-azure-databricks-workspace"></a>Создание рабочей области Azure Databricks

В этом разделе вы создадите рабочую область Azure Databricks с помощью [портала Azure](https://portal.azure.com/).

1. На портале Azure выберите **Создать ресурс** > **Analytics** > **Azure Databricks**.

    ![Databricks на портале Azure](../media/tutorials/azure-databricks-on-portal.png "Databricks on Azure portal")

3. В разделе **Служба Azure Databricks** укажите следующие значения, чтобы создать рабочую область Databricks:


    |Свойство  |ОПИСАНИЕ  |
    |---------|---------|
    |**Имя рабочей области**     | Укажите имя рабочей области Databricks.        |
    |**Подписка**     | Выберите подписку Azure в раскрывающемся списке.        |
    |**Группа ресурсов**     | Укажите, следует ли создать новую группу ресурсов или использовать имеющуюся. Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. Дополнительные сведения см. в [обзоре группы ресурсов Azure](../../../azure-resource-manager/resource-group-overview.md). |
    |**Местоположение.**     | Выберите **восточная часть США 2** или один из доступных регионов. Доступные регионы см. в статье о [доступности служб Azure по регионам](https://azure.microsoft.com/regions/services/).        |
    |**Ценовая категория**     |  Вы можете выбрать уровень **Стандартный** или **Премиум**. НЕ выбирайте **Пробная версия**. Дополнительные сведения об этих ценовых категориях см. на [странице цен на Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Нажмите кнопку **Создать**.

4. Создание рабочей области займет несколько минут. 

## <a name="create-a-spark-cluster-in-databricks"></a>Создание кластера Spark в Databricks

1. На портале Azure перейдите к созданной рабочей области Databricks, а затем выберите **Launch Workspace** (Запуск рабочей области).

2. Вы будете перенаправлены на портал Azure Databricks. На портале выберите **Создать кластер**.

    ![Databricks в Azure](../media/tutorials/databricks-on-azure.png "Databricks on Azure")

3. На странице **создания кластера** укажите значения для создания кластера.

    ![Создание кластера Databricks Spark в Azure](../media/tutorials/create-databricks-spark-cluster.png "Create Databricks Spark cluster on Azure")

    Для всех остальных параметров, кроме следующих, примите значения по умолчанию:

   * Введите имя кластера.
   * В рамках этой статьи создайте кластер со средой выполнения **5.2**. НЕ выбирайте среду выполнения **5.3**.
   * Убедитесь, что установлен флажок **Terminate after \_\_ minutes of inactivity** (Завершить через \_\_ минут бездействия). Укажите длительность (в минутах) для завершения работы кластера, если тот не используется.

     Выберите **Create cluster** (Создать кластер). 
4. Создание кластера занимает несколько минут. После запуска кластера можно вложить записные книжки в кластер и запустить задания Spark.

## <a name="create-a-twitter-application"></a>Создание приложения Twitter

Для получения потока твитов создайте приложение в Twitter. Следуйте инструкциям по созданию приложения Twitter и запишите значения, необходимые для выполнения заданий из этого руководства.

1. В веб-браузере перейдите в раздел [Twitter Application Management](https://apps.twitter.com/) (Управление приложением Twitter) и выберите **Создание приложения**.

    ![Создание приложения Twitter](../media/tutorials/databricks-create-twitter-app.png "Create Twitter application")

2. На странице **Create an application** (Создание приложения) укажите сведения для нового приложения, а затем выберите **Create your Twitter application** (Создать приложение Twitter).

    ![Подробные сведения о приложении Twitter](../media/tutorials/databricks-provide-twitter-app-details.png "Twitter application details")

3. На странице приложения перейдите на вкладку **Keys and Access Tokens** (Ключи и маркеры доступа) и скопируйте значения **ключа потребителя** и **секрета потребителя**. Кроме того, выберите **Create my access token** (Создать маркер доступа), чтобы создать маркеры доступа. Скопируйте значения **маркера доступа** и **секрета маркера доступа**.

    ![Подробные сведения о приложении Twitter](../media/tutorials/twitter-app-key-secret.png "Twitter application details")

Сохраните значения, полученные для приложения Twitter. Они понадобятся вам позже при работе с этим руководством.

## <a name="attach-libraries-to-spark-cluster"></a>Подключение библиотек к кластеру Spark

В этом руководстве для отправки твитов в Центры событий используются API-интерфейсы Twitter. Для чтения и записи данных в Центрах событий Azure используется [соединитель Центров событий Apache Spark](https://github.com/Azure/azure-event-hubs-spark). Чтобы использовать эти API-интерфейсы в рамках кластера, добавьте их в Azure Databricks в качестве библиотек, а затем свяжите их с кластером Spark. Ниже показано, как добавить библиотеки в папку **Общая** в рабочей области.

1. В рабочей области Azure Databricks выберите **Рабочая область** и щелкните правой кнопкой мыши **Shared** (Общая). В контекстном меню выберите **Создать** > **Библиотека**.

   ![Диалоговое окно добавления библиотеки](../media/tutorials/databricks-add-library-option.png "Add library dialog box")

2. На странице новой библиотеки для параметра **Источник** выберите **Maven**. В поле **Координаты** введите координаты пакета, который требуется добавить. Ниже указаны координаты Maven для библиотек, используемых в рамках этого руководства.

   * Соединитель Центров событий Spark — `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * API Twitter — `org.twitter4j:twitter4j-core:4.0.7`

     ![Предоставление координат Maven](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Provide Maven coordinates")

3. Нажмите кнопку **Создать**.

4. Выберите папку, в которую добавлена библиотека, а затем выберите имя библиотеки.

    ![Выбор добавляемой библиотеки](../media/tutorials/select-library.png "Select library to add")

5. Если на странице библиотеки нет кластера, выберите **Кластеры** и запустите созданный кластер. Дождитесь, когда состояние перейдет в значение "Выполняется", и вернитесь на страницу библиотеки.
На странице библиотеки выберите кластер, в котором нужно использовать библиотеку, а затем щелкните **Установить**. Сразу после успешного установления связи между библиотекой и кластером состояние библиотеки изменяется на **Установлено**.

    ![Установка библиотеки в кластере](../media/tutorials/databricks-library-attached.png "Установка библиотеки в кластере")

6. Повторите эти действия для пакета Twitter `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Получение ключа доступа Cognitive Services

В этом учебнике для выполнения обнаружения аномалий в потоке твитов практически в реальном времени используются [API Детектора аномалий Azure Cognitive Services](../overview.md). Прежде чем использовать API, необходимо создать ресурс "Детектор аномалий" в Azure и получить ключ доступа к API Детектора аномалий.

1. Войдите на [портале Azure](https://portal.azure.com/).

2. Выберите действие **Создать ресурс**.

3. В разделе Azure Marketplace щелкните **Искусственный интеллект и машинное обучение** > **Просмотреть все** > **Cognitive Services — Больше** > **Детектор аномалий**. Вы также можете использовать [эту ссылку](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) непосредственно на диалоговое окно **Создать**.

    ![Создание ресурса "Детектор аномалий"](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Создание ресурса \"Детектор аномалий\"")

4. В диалоговом окне **Создать** введите следующие значения.

    |Значение |ОПИСАНИЕ  |
    |---------|---------|
    |ИМЯ     | Имя ресурса "Детектор аномалий".        |
    |Подписка     | Подписка Azure, с которой будет связан ресурс.        |
    |Расположение     | Расположение Azure.        |
    |Ценовая категория     | Ценовая категория службы. Дополнительные сведения о ценах на Детектор аномалий см. на [странице расценок](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Группа ресурсов     | Укажите, следует ли создать новую группу ресурсов или использовать существующую.        |


     Нажмите кнопку **Создать**.

5. После создания ресурса со вкладки **Обзор** скопируйте и сохраните URL-адрес **конечной точки**, как показано на снимке экрана. Затем выберите **Показать ключи доступа**.

    ![Показать ключи доступа](../media/tutorials/cognitive-services-get-access-keys.png "Show access keys")

6. В разделе **Ключи** нажмите значок копирования возле выбранного ключа. Сохраните ключ доступа.

    ![Копирование ключей доступа](../media/tutorials/cognitive-services-copy-access-keys.png "")

## <a name="create-notebooks-in-databricks"></a>Создание записных книжек в Databricks

В этом разделе в рабочей области Databricks создается две записные книжки со следующими именами.

- **SendTweetsToEventHub** — записная книжка производителя, которая используется для получения твитов из приложения Twitter и для их потоковой передачи в Центры событий.
- **AnalyzeTweetsFromEventHub** — клиентская записная книжка объекта-получателя для чтения твитов из Центров событий и выполнения обнаружения аномалий.

1. В рабочей области Azure Databricks на левой панели выберите **Рабочая область**. В раскрывающемся списке **Рабочая область** выберите **Создать**, а затем **Записная книжка**.

    ![Создание записной книжки в Databricks](../media/tutorials/databricks-create-notebook.png "Create notebook in Databricks")

2. В диалоговом окне **создания записной книжки** введите имя **SendTweetsToEventHub**, выберите **Scala** в качестве языка, а затем выберите созданный ранее кластер Spark.

    ![Создание записной книжки в Databricks](../media/tutorials/databricks-notebook-details.png "Create notebook in Databricks")

    Нажмите кнопку **Создать**.

3. Повторите эти шаги для создания записной книжки **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Отправка твитов в Центры событий.

В записной книжке **SendTweetsToEventHub** вставьте приведенный ниже код и замените заполнители значениями вашего пространства имен Центров событий и созданного ранее приложения Twitter. Эта записная книжка извлекает время создания и количество отметок "Нравится" из твитов с ключевым словом "Azure" и выполняет потоковую передачу этих данных о событиях в Центры событий в режиме реального времени.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

Чтобы запустить записную книжку, нажмите клавиши **SHIFT+ВВОД**. Вывод должен выглядеть также, как показано в следующем фрагменте кода. Каждое событие в выходных данных представляет собой комбинацию метки времени создания и количества отметок "Нравится", передающихся в Центры событий.

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Чтение твитов из Центров событий.

В записной книжке **AnalyzeTweetsFromEventHub** вставьте следующий код и замените заполнители значениями созданного ранее ресурса "Детектор аномалий". Эта записная книжка считывает твиты, переданные ранее в Центры событий с помощью записной книжки **SendTweetsToEventHub**.

Сначала создайте клиента для вызова Детектора аномалий. 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

Чтобы запустить записную книжку, нажмите клавиши **SHIFT+ВВОД**. Вывод должен выглядеть также, как показано в следующем фрагменте кода.

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

Затем подготовьте статистическую функцию для использования в дальнейшем.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

Чтобы запустить записную книжку, нажмите клавиши **SHIFT+ВВОД**. Вывод должен выглядеть также, как показано в следующем фрагменте кода.

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

Затем загрузите данные из концентратора событий для обнаружения аномалий. Замените заполнители значениями созданных ранее Центров событий Azure.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

Выходные данные будут выглядеть следующим образом. Обратите внимание, что ваша дата в таблице может отличаться от даты в этом учебнике, так как данные находятся в режиме реального времени.
![Загрузка данных из концентратора событий](../media/tutorials/load-data-from-eventhub.png "Загрузка данных из концентратора событий")

Таким образом мы почти в реальном времени передаем потоком данные из Центров событий Azure в Azure Databricks, используя соединитель Центров событий для Apache Spark. Дополнительные сведения о том, как использовать соединитель Центров событий для Spark, см. в [документации соединителя](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Выполнение обнаружения аномалий в твитах

В этом разделе выполняется обнаружение аномалий в твитах, полученных с помощью API Детектора аномалий. В этом разделе добавляется фрагменты кода к той же записной книжке **AnalyzeTweetsFromEventHub**.

Чтобы выполнить обнаружение аномалий, сперва вам потребуется выполнить статистическое вычисление показателей метрик по часам.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
Выходные данные будут выглядеть, как следующий фрагмент.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Затем выведите агрегированный результат выходных данных в Delta. Так как для обнаружения аномалий требуется большое окно журнала, мы используем Delta для сохранения данных журнала для точки, для которой требуется выполнить обнаружение. Замените [Placeholder: table name] на полное созданное имя таблицы Delta (например, tweets). Замените [Placeholder: folder name for checkpoints] на строковое значение, которое каждый раз уникально при выполнении этого кода (например, etl-from-eventhub-20190605).
Чтобы узнать больше о Delta Lake в Azure Databricks, ознакомьтесь с руководством [здесь](https://docs.azuredatabricks.net/delta/index.html).


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

Замените [Placeholder: table name] на то же имя таблицы Delta, которое выбрано выше.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
Выходные данные будут выглядеть так: 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

Теперь агрегированные данные временных рядов непрерывно поступают в Delta. Затем вы можете запланировать почасовое задание для обнаружения аномалий последней точки. Замените [Placeholder: table name] на то же имя таблицы Delta, которое выбрано выше.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Результат будет выглядеть так: 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

Вот и все! С помощью Azure Databricks вы успешно выполнили потоковую передачу данных в Центры событий Azure, использовали данные потока в соединителе Центров событий Azure и выполнили обнаружение аномалий в потоковых данных практически в реальном времени.
Хотя в этом учебнике используется почасовая степень детализации, вы всегда можете изменить степень детализации для соответствия вашим потребностям. 

## <a name="clean-up-resources"></a>Очистка ресурсов

После выполнения заданий из этого руководства вы можете завершить работу кластера. Для этого в рабочей области Azure Databricks на левой панели выберите **Кластеры**. Для кластера, работу которого необходимо завершить, переместите указатель мыши на многоточие в столбце **Действия** и выберите значок **Завершить**, а затем **Подтвердить**.

![Завершение работы кластера Databricks](../media/tutorials/terminate-databricks-cluster.png "Stop a Databricks cluster")

Если не завершить работу кластера вручную, она завершится автоматически, если во время создания кластера вы установили флажок **Terminate after \_\_ minutes of inactivity** (Завершать работу после \_\_ мин бездействия). В этом случае работа кластера должна завершиться автоматически, если кластер был неактивным в течение определенного времени.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как с помощью Azure Databricks выполнить потоковую передачу данных в Центры событий Azure с последующим чтением данных потоковой передачи из Центров событий в реальном времени. Перейдите к следующему учебнику, чтобы узнать, как вызвать API Детектора аномалий и визуализировать аномалии с помощью Power BI Desktop. 

> [!div class="nextstepaction"]
>[Пакетное обнаружение аномалий с помощью Power BI Desktop](batch-anomaly-detection-powerbi.md)
