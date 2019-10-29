---
title: Руководство по Обработка данных из Центров событий Azure с помощью Apache Spark в HDInsight
description: Руководство. Подключение Apache Spark в Azure HDInsight к Центрам событий Azure и потоковая передача данных
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/24/2019
ms.openlocfilehash: be21b809272a132ee6e63582036c36ad5dcdf4ad
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "71266199"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Руководство по Обработка твитов с помощью Центров событий Azure и Apache Spark в HDInsight

В этом руководстве показано, как создать приложение потоковой передачи [Apache Spark](https://spark.apache.org/) для отправки твитов в центр событий Azure, а также приложение для чтения твитов из центра событий. Подробное описание потоковой передачи Spark см. в [этом разделе](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight предоставляет аналогичные функции потоковой передачи для кластера Spark в Azure.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * отправка сообщений в концентратор событий Azure;
> * чтение сообщений из концентратора событий Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Spark в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Создание кластера Apache Spark в HDInsight с помощью шаблона](./apache-spark-jupyter-spark-sql-use-portal.md).

* Опыт работы с записными книжками Jupyter с Spark в HDInsight. Дополнительные сведения см. в статье [Руководство. Загрузка данных и выполнение запросов в кластере Apache Spark в Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Учетная запись Twitter](https://twitter.com/i/flow/signup).

## <a name="create-a-twitter-application"></a>Создание приложения Twitter

Для получения потока твитов создайте приложение в Twitter. Следуйте инструкциям по созданию приложения Twitter и запишите значения, необходимые для выполнения заданий из этого руководства.

1. Перейдите на [страницу управления приложениями Twitter](https://apps.twitter.com/).

1. Выберите **Create New App** (Создать приложение).

1. Укажите следующие значения.

    |Свойство |Значение |
    |---|---|
    |ИМЯ|Укажите имя приложения. В этом руководстве используется имя **HDISparkStreamApp0423**. Имя должно быть уникальным.|
    |ОПИСАНИЕ|Дайте краткое описание приложения. В этом руководстве используется следующее описание: **Простое приложение потоковой передачи HDInsight Spark**.|
    |Веб-сайт|Укажите веб-сайт приложения. Необязательно указывать действительный веб-сайт.  В этом руководстве используется значение `http://www.contoso.com`.|
    |URL-адрес обратного вызова|Можно оставить пустым.|

1. Выберите **Yes, I have read and agree to the Twitter Developer Agreement** (Я подтверждаю ознакомление и согласие с соглашением с разработчиком Twitter), а затем выберите **Create your Twitter application** (Создать приложение Twitter).

1. Откройте вкладку **Ключи и токены доступа** .

1. В конце страницы выберите **Create my access token** (Создать маркер доступа).

1. Запишите следующие значения со страницы.  Они понадобятся вам позже при работе с этим руководством.

    - **Ключ пользователя (ключ API)** .    
    - **Секрет пользователя (секрет API)** .  
    - **Маркер доступа**
    - **Секрет маркера доступа**.   

## <a name="create-an-azure-event-hubs-namespace"></a>Создание пространства имен Центров событий Azure

Используйте этот концентратор событий для хранения твитов.

1. Войдите на [портале Azure](https://portal.azure.com). 

2. В меню слева выберите **Все службы**.  

3. В разделе **ИНТЕРНЕТ ВЕЩЕЙ** выберите **Центры событий**. 

    ![Создание концентратора событий для примера потоковой передачи Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Создание концентратора событий для примера потоковой передачи Spark")

4. Щелкните **+ Добавить**.

5. Для пространства имен нового Центра событий введите следующие значения.

    |Свойство |Значение |
    |---|---|
    |ИМЯ|Введите имя для Центра событий.  В этом руководстве используется имя **myeventhubns20180403**.|
    |Ценовая категория|Выберите **Стандартная**.|
    |Subscription|Выберите соответствующую подписку.|
    |группа ресурсов.|Выберите имеющуюся группу ресурсов из раскрывающегося списка или щелкните вариант **Создать**, чтобы создать новую группу ресурсов.|
    |Location|Для сокращения задержек и затрат выберите то же **расположение**, в котором находится кластер Apache Spark в HDInsight.|
    |Включить автоматическое расширение (необязательно) |Автоматическое расширение автоматически масштабирует число единиц пропускной способности, которые назначены для пространства имен Центров событий, если объем трафика превышает его.  |
    |Автоматически увеличивать максимальное число единиц пропускной способности (необязательно)|Этот ползунок будет отображаться только в том случае, если вы установили флажок **Включить автоматическое расширение**.  |

    ![Указание имени концентратора событий для примера потоковой передачи Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Указание имени концентратора событий для примера потоковой передачи Spark")

6. Выберите **Создать**, чтобы создать пространство имен.  Развертывание займет несколько минут.

## <a name="create-an-azure-event-hub"></a>Создание Центра событий Azure
Создайте Центр событий после развертывания пространства имен Центров событий.  На портале

1. В меню слева выберите **Все службы**.  

1. В разделе **ИНТЕРНЕТ ВЕЩЕЙ** выберите **Центры событий**.  

1. Выберите пространство имен Центров событий из списка.  

1. На странице **Пространство имен Центров событий** выберите **+ Концентратор событий**.  
1. Введите следующие значения на странице **Создать Центр событий**.

    - **Имя.** Задайте имя для этого Центра событий. 
 
    - **Количество разделов**: 10.  

    - **Хранение сообщений**: 1.   
   
      ![Указание сведений о концентраторе событий для примера потоковой передачи Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Указание сведений о концентраторе событий для примера потоковой передачи Spark")

1. Нажмите кнопку **Создать**.  Развертывание завершится через несколько секунд, и вы вернетесь на страницу пространства имен Центров событий.

1. В разделе **Параметры** выберите **Политики общего доступа**.

1. Выберите **RootManageSharedAccessKey**.
    
     ![Определение политик концентратора событий для примера потоковой передачи Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Определение политик концентратора событий для примера потоковой передачи Spark")

1. Сохраните значения **Первичный ключ** и **Строка подключения — первичный ключ**. Они понадобятся позже для работы с этим руководством.

     ![Просмотр ключей политик концентратора событий для примера потоковой передачи Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Просмотр ключей политик концентратора событий для примера потоковой передачи Spark")


## <a name="send-tweets-to-the-event-hub"></a>Отправка твитов в концентратор событий

Создайте записную книжку Jupyter с именем **SendTweetsToEventHub**. 

1. Добавьте внешние библиотеки Apache Maven, выполнив следующий код:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Измените приведенный ниже код, заменив `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>`, `<CONSUMER SECRET>`, `<ACCESS TOKEN>` и `<TOKEN SECRET>` соответствующими значениями. Отправьте твиты в центр событий, выполнив исправленный код:

    ```scala
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Откройте концентратор событий на портале Azure.  На странице **обзора** вы увидите несколько диаграмм, отображающих сообщения, отправленные в концентратор событий.

## <a name="read-tweets-from-the-event-hub"></a>Чтение твитов из концентратора событий

Создайте другую записную книжку Jupyter с именем **ReadTweetsFromEventHub**. 

1. Добавьте внешнюю библиотеку Apache Maven, выполнив следующий код:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13"}}
    ```

2. Измените приведенный ниже код, заменив `<Event hub name>` и `<Event hub namespace connection string>` соответствующими значениями. Считайте твиты из центра событий, выполнив исправленный код:

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

В случае с HDInsight ваши данные хранятся в службе хранилища Azure или Azure Data Lake Storage, что позволяет безопасно удалить неиспользуемый кластер. Плата за кластеры HDInsight взимается, даже когда они не используются. Если вы планируете сразу приступить к следующему руководству, можно оставить кластер. Либо продолжите и удалите кластер.

Откройте кластер на портале Azure и выберите **Удалить**.

![Удаление кластера HDInsight на портале Azure](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Удаление кластера HDInsight")

Кроме того, можно выбрать имя группы ресурсов, чтобы открыть страницу группы ресурсов, а затем щелкнуть **Удалить группу ресурсов**. Вместе с группой ресурсов вы также удалите кластер Spark в HDInsight и учетную запись хранения по умолчанию.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создавать приложение потоковой передачи Apache Spark для отправки твитов в центр событий Azure, а также приложение для чтения твитов из центра событий.  Перейдите к следующей статье, чтобы создать приложение машинного обучения.

> [!div class="nextstepaction"]
> [Создание приложений машинного обучения Apache Spark в Azure HDInsight](./apache-spark-ipython-notebook-machine-learning.md)
