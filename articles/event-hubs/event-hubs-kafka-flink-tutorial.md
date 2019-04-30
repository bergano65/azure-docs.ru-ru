---
title: Использование Apache Flink для Apache Kafka с Центрами событий Azure | Документация Майкрософт
description: Эта статья содержит сведения о том, как подключить Apache Flink к Центрам событий Azure с поддержкой Apache Kafka
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: dc4a982dde62f62eb8f2d91a61fd70ba79fa13d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821433"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Использование Apache Flink с Центрами событий Azure для Apache Kafka
В этом руководстве показано, как выполнять подключение Apache Flink к концентраторам событий с поддержкой Kafka без необходимости менять клиенты протоколов или запускать собственные кластеры. Центры событий Azure поддерживают [Apache Kafka 1.0.](https://kafka.apache.org/10/documentation.html)

Одним из ключевых преимуществ использования платформы Apache Kafka является экосистема платформ, к которым она может подключиться. Центры событий с поддержкой Kafka сочетают в себе гибкость Kafka с масштабируемостью, согласованностью и поддержкой экосистемы Azure.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Создание пространства имен в Центрах событий
> * Клонирование примера проекта
> * Запуск производителя Flink 
> * Запуск потребителя Flink

> [!NOTE]
> Этот пример можно найти на сайте [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink).

## <a name="prerequisites"></a>Технические условия

Для работы с этим руководством выполните следующие предварительные требования:

* Прочтите статью [Центры событий Azure для Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начать работу.
* [Комплект разработчика Java (JDK 1.7+)](https://aka.ms/azure-jdks)
    * В Ubuntu выполните команду `apt-get install default-jdk`, чтобы установить JDK.
    * Обязательно настройте переменную среды JAVA_HOME так, чтобы она указывала на папку, в которой установлен пакет JDK.
* [Скачайте](https://maven.apache.org/download.cgi) и [установите](https://maven.apache.org/install.html) двоичный архив Maven.
    * В Ubuntu выполните команду `apt-get install maven`, чтобы установить Maven.
* [Git](https://www.git-scm.com/downloads)
    * В Ubuntu выполните команду `sudo apt-get install git`, чтобы установить Git.

## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен в Центрах событий

Для отправки и получения данных из любой службы концентраторов событий требуется пространство имен концентраторов событий. Инструкции по получению конечной точки Kafka Центров событий см. в статье [Создание Центров событий с поддержкой Kafka](event-hubs-create-kafka-enabled.md). Скопируйте строку подключения к Центрам событий для дальнейшего использования.

## <a name="clone-the-example-project"></a>Клонирование примера проекта

Теперь, когда у вас есть строка подключения к Центрам событий с поддержкой Kafka, клонируйте репозиторий Центров событий Azure для Kafka и перейдите ко вложенной папке `flink`.

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Запуск производителя Flink

Используя предоставленный пример производителя Flink, отправьте сообщения в службу Центров событий.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Предоставление конечной точки Kafka в Центрах событий

#### <a name="producerconfig"></a>producer.config

Обновите значения `bootstrap.servers` и `sasl.jaas.config` в `producer/src/main/resources/producer.config`, чтобы перенаправить производителя на конечную точку Kafka Центров событий с правильной аутентификацией.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Запуск производителя из командной строки

Чтобы запустить производитель из командной строки, создайте JAR-файл, а затем запустите его из Maven (или создайте JAR-файл с помощью Maven, затем запустите его в Java, добавив необходимые JAR-файлы Kafka в путь к классу):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Производитель начнет отправлять события в концентратор событий с поддержкой Kafka в раздел `test` и выводить события в STDOUT.

## <a name="run-flink-consumer"></a>Запуск потребителя Flink

Используя предоставленный пример потребителя, получите сообщения от Центров событий с поддержкой Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Предоставление конечной точки Kafka в Центрах событий

#### <a name="consumerconfig"></a>consumer.config

Обновите значения `bootstrap.servers` и `sasl.jaas.config` в `consumer/src/main/resources/consumer.config`, чтобы перенаправить потребителя на конечную точку Kafka в Центрах событий с правильной аутентификацией.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Запуск потребителя из командной строки

Чтобы запустить потребителя из командной строки, создайте JAR-файл, а затем запустите его из Maven (или создайте JAR-файл с помощью Maven, затем запустите его в Java, добавив необходимые JAR-файлы Kafka в путь к классу):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Если в концентраторе событий с поддержкой Kafka есть события (например, если ваш производитель также работает), потребитель начинает получать события из раздела `test`.

Дополнительные сведения о подключении Flink к Kafka см. в [руководстве по соединителю Kafka Flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html).

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнять подключение Apache Flink к концентраторам событий с поддержкой Kafka без необходимости менять клиенты протоколов или запускать собственные кластеры. В рамках данного руководства были выполнены следующие шаги. 

> [!div class="checklist"]
> * Создание пространства имен в Центрах событий
> * Клонирование примера проекта
> * Запуск производителя Flink 
> * Запуск потребителя Flink

Дополнительные сведения о Центрах событий и Центрах событий для Kafka см. в статьях:  

- [Сведения о Центрах событий](event-hubs-what-is-event-hubs.md)
- [Центры событий Azure для Apache Kafka (предварительная версия)](event-hubs-for-kafka-ecosystem-overview.md)
- [Создание концентраторов событий с поддержкой Kafka](event-hubs-create-kafka-enabled.md)
- [Потоковая передача данных в Центры событий из приложений Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Использование Apache Kafka MirrorMaker с Центрами событий](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connect your Apache Spark application with Kafka-enabled Azure Event Hubs](event-hubs-kafka-spark-tutorial.md) (Подключение приложения Apache Spark к Центрам событий Azure с поддержкой Kafka)
- [Интеграция поддержки Apache Kafka Connect в Центрах событий Azure (предварительная версия)](event-hubs-kafka-connect-tutorial.md)
- [Использование Akka Streams с Центрами событий для Apache Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Migrating to Azure Event Hubs for Apache Kafka Ecosystems](https://github.com/Azure/azure-event-hubs-for-kafka) (Переход в Центры событий Azure для экосистем Apache Kafka)
