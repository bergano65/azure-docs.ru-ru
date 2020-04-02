---
title: Использование Apache Flink для Apache Kafka с Центрами событий Azure | Документация Майкрософт
description: В этой статье приводится информация о том, как подключить Apache Flink к центру событий Azure
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6ab542e1328bb986f53d31e2eca75007cf1e0c75
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521799"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Использование Apache Flink с Центрами событий Azure для Apache Kafka
В этом учебнике показано, как подключить Apache Flink к центру событий без изменения клиентских протоколов или запуска собственных кластеров. Центры событий Azure поддерживают [Apache Kafka 1.0.](https://kafka.apache.org/10/documentation.html)

Одним из ключевых преимуществ использования платформы Apache Kafka является экосистема платформ, к которым она может подключиться. Центры событий сочетают гибкость Kafka с масштабируемостью, согласованностью и поддержкой экосистемы Azure.

В этом руководстве вы узнаете, как:
> [!div class="checklist"]
> * Создание пространства имен в Центрах событий
> * Клонирование примера проекта
> * Запуск производителя Flink 
> * Запуск потребителя Flink

> [!NOTE]
> Этот пример можно найти на сайте [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством выполните следующие предварительные требования:

* Прочтите статью [Центры событий Azure для Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начать работу.
* [Комплект разработки Java (JDK) 1.7](https://aka.ms/azure-jdks)
    * В Ubuntu выполните команду `apt-get install default-jdk`, чтобы установить JDK.
    * Обязательно настройте переменную среды JAVA_HOME так, чтобы она указывала на папку, в которой установлен пакет JDK.
* [Скачать](https://maven.apache.org/download.cgi) и [установить](https://maven.apache.org/install.html) двоичный архив Maven
    * В Ubuntu выполните команду `apt-get install maven`, чтобы установить Maven.
* [Git](https://www.git-scm.com/downloads);
    * В Ubuntu выполните команду `sudo apt-get install git`, чтобы установить Git.

## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен в Центрах событий

Для отправки и получения данных из любой службы концентраторов событий требуется пространство имен концентраторов событий. Смотрите [Создание концентратора событий](event-hubs-create.md) для инструкций по созданию пространства имен и концентратора событий. Скопируйте строку подключения к Центрам событий для дальнейшего использования.

## <a name="clone-the-example-project"></a>Клонирование примера проекта

Теперь, когда у вас есть строка подключения концентраторов событий, клоните концентратор azure Event для репозитория Kafka и перейдите к `flink` подфондеру:

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

Теперь производитель начнет отправлять события в `test` центр событий по теме и печатать события для уступки.

## <a name="run-flink-consumer"></a>Запуск потребителя Flink

Используя предоставленный пример потребителя, получайте сообщения из концентратора событий. 

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

Если в концентраторе событий есть события (например, если ваш производитель `test`также работает), то потребитель теперь начинает получать события из темы .

Дополнительные сведения о подключении Flink к Kafka см. в [руководстве по соединителю Kafka Flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html).

## <a name="next-steps"></a>Дальнейшие действия
В этом уроке вы узнали, как подключить Apache Flink к концентратам событий без изменения ваших клиентов протокола или запуска собственных кластеров. В рамках данного руководства были выполнены следующие шаги. 

> [!div class="checklist"]
> * Создание пространства имен в Центрах событий
> * Клонирование примера проекта
> * Запуск производителя Flink 
> * Запуск потребителя Flink

Дополнительные сведения о Центрах событий и Центрах событий для Kafka см. в статьях:  

- [Сведения о Центрах событий](event-hubs-what-is-event-hubs.md)
- [Центры событий Azure для Apache Kafka (предварительная версия)](event-hubs-for-kafka-ecosystem-overview.md)
- [Создание концентратора событий](event-hubs-create.md)
- [Потоковая передача данных в Центры событий из приложений Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Зеркальное отображение брокера Kafka в концентраторе событий](event-hubs-kafka-mirror-maker-tutorial.md)
- [Подключение Apache Spark к концентратору событий](event-hubs-kafka-spark-tutorial.md)
- [Интеграция Kafka Connect с центром событий](event-hubs-kafka-connect-tutorial.md)
- [Подключение Потоков Akka к центру событий](event-hubs-kafka-akka-streams-tutorial.md)
- [Migrating to Azure Event Hubs for Apache Kafka Ecosystems](https://github.com/Azure/azure-event-hubs-for-kafka) (Переход в Центры событий Azure для экосистем Apache Kafka)
