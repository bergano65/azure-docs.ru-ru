---
title: Подключение приложения Apache Spark с помощью Центров событий Azure с поддержкой Kafka | Документация Майкрософт
description: Используйте Apache Spark с Центрами событий Azure для Kafka.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: ''
ms.date: 10/30/2018
ms.author: bahariri
ms.openlocfilehash: 2a9f1ea069bdb45adb1b8c6b52392f15a4660b5c
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285124"
---
# <a name="connect-your-apache-spark-application-with-kafka-enabled-azure-event-hubs"></a>Подключение приложения Apache Spark с помощью Центров событий Azure с поддержкой Kafka
В этом руководстве описывается, как подключить приложение Spark к Центрам событий с поддержкой Kafka для потоковой передачи в режиме реального времени. Такая интеграция обеспечивает потоковую передачу без необходимости изменять клиенты протокола или запускать собственные кластеры Kafka или Zookeeper. Для работы с этим руководством требуется Apache Spark v2.4+ и Apache Kafka v2.0+.

> [!NOTE]
> Этот пример можно найти на сайте [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/).

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Создание пространства имен в Центрах событий
> * Клонирование примера проекта
> * Запуск Spark.
> * Чтение из Центров событий для Kafka.
> * Запись в Центры событий для Kafka.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с данным руководством необходимо выполнить описанные ниже условия.
-   Подписка Azure. Если ее нет, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/).
-   [Apache Spark v2.4](https://spark.apache.org/downloads.html).
-   [Apache Kafka v2.0]( https://kafka.apache.org/20/documentation.html).
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> Адаптер Spark-Kafka был обновлен для поддержки Kafka v2.0, начиная со Spark v2.4. В предыдущих выпусках Spark адаптер поддерживал Kafka v0.10 и более поздние версии, но в основном как база использовались API Kafka v0.10. Так как Центры событий для Kafka не поддерживают Kafka v0.10, адаптеры Spark-Kafka от версий Spark до версии 2.4 не поддерживаются Центрами событий для экосистем Kafka.


## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен в Центрах событий
Для отправки и получения данных из любой службы Центров событий требуется пространство имен Центров событий. Инструкции о получении конечной точки Kafka концентраторов событий см. в статье [Creating a Kafka enabled Event Hub](event-hubs-create.md) (Создание концентраторов событий с поддержкой Kafka). Получите строку подключения Центров событий и полное доменное имя (FQDN) для последующего использования. Инструкции см. в статье [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Получение строки подключения для Центров событий). 

## <a name="clone-the-example-project"></a>Клонирование примера проекта
Клонируйте репозиторий Центров событий Azure и перейдите к вложенной папке `tutorials/spark`:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Чтение из Центров событий для Kafka
Внеся несколько изменений в конфигурации, вы сможете запустить чтение из Центров событий для Kafka. Обновите **BOOTSTRAP_SERVERS** и **EH_SASL** сведениями о пространстве имен, чтобы запустить потоковую передачу с помощью Центров событий так же, как с Kafka. Чтобы ознакомиться с полным примером кода, просмотрите файл sparkConsumer.scala на сайте GitHub. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "false")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Запись в Центры событий для Kafka
Вы также можете выполнить запись в Центры событий таким же образом, как и в Kafka. Обновите конфигурацию, чтобы заменить сведения в **BOOTSTRAP_SERVERS** и **EH_SASL** сведениями о пространстве имен в Центре событий.  Чтобы ознакомиться с полным примером кода, просмотрите файл sparkProducer.scala на сайте GitHub. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как выполнить потоковую передачу с помощью соединителя Spark-Kafka и Центров событий для Kafka. Вы выполнили следующие шаги: 

> [!div class="checklist"]
> * Создание пространства имен в Центрах событий
> * Клонирование примера проекта
> * Запуск Spark.
> * Чтение из Центров событий для Kafka.
> * Запись в Центры событий для Kafka.

Дополнительные сведения о Центрах событий и Центрах событий для Kafka см. в статьях:  

- [Сведения о Центрах событий](event-hubs-what-is-event-hubs.md)
- [Центры событий Azure для Apache Kafka (предварительная версия)](event-hubs-for-kafka-ecosystem-overview.md)
- [Создание концентраторов событий с поддержкой Kafka](event-hubs-create-kafka-enabled.md)
- [Потоковая передача данных в Центры событий из приложений Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Использование Apache Kafka MirrorMaker с Центрами событий](event-hubs-kafka-mirror-maker-tutorial.md)
- [Использование Apache Flink с Центрами событий Azure для Apache Kafka](event-hubs-kafka-flink-tutorial.md)
- [Интеграция поддержки Apache Kafka Connect в Центрах событий Azure (предварительная версия)](event-hubs-kafka-connect-tutorial.md)
- [Использование Akka Streams с Центрами событий для Apache Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Migrating to Azure Event Hubs for Apache Kafka Ecosystems](https://github.com/Azure/azure-event-hubs-for-kafka) (Переход в Центры событий Azure для экосистем Apache Kafka)