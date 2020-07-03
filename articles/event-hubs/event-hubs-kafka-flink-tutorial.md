---
title: Использование Apache Flink для Apache Kafka с Центрами событий Azure | Документация Майкрософт
description: В этой статье содержатся сведения о подключении Apache Флинк к концентратору событий Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: how-to
ms.date: 04/02/2020
ms.author: shvija
ms.openlocfilehash: 2e5a2924cdc00c1cc057d71c40645085df4bae6a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632815"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Использование Apache Flink с Центрами событий Azure для Apache Kafka
В этом руководстве показано, как подключить Apache Флинк к концентратору событий, не изменяя Клиенты протокола или не запуская собственные кластеры. Концентраторы событий Azure поддерживают [Apache Kafka версии 1,0.](https://kafka.apache.org/10/documentation.html).

Одним из ключевых преимуществ использования платформы Apache Kafka является экосистема платформ, к которым она может подключиться. Концентраторы событий объединяют гибкость Kafka с масштабируемостью, согласованностью и поддержкой экосистемы Azure.

В этом руководстве описано следующее:
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
* [Пакет Java Development Kit (JDK) 1.7 +](https://aka.ms/azure-jdks)
    * В Ubuntu выполните команду `apt-get install default-jdk`, чтобы установить JDK.
    * Обязательно настройте переменную среды JAVA_HOME так, чтобы она указывала на папку, в которой установлен пакет JDK.
* [Скачивание](https://maven.apache.org/download.cgi) и [Установка](https://maven.apache.org/install.html) двоичного архива Maven
    * В Ubuntu выполните команду `apt-get install maven`, чтобы установить Maven.
* [Git](https://www.git-scm.com/downloads)
    * В Ubuntu выполните команду `sudo apt-get install git`, чтобы установить Git.

## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен в Центрах событий

Для отправки и получения данных из любой службы концентраторов событий требуется пространство имен концентраторов событий. Инструкции по созданию пространства имен и концентратора событий см. в разделе [Создание концентратора событий](event-hubs-create.md) . Скопируйте строку подключения к Центрам событий для дальнейшего использования.

## <a name="clone-the-example-project"></a>Клонирование примера проекта

Теперь, когда у вас есть строка подключения концентраторов событий, выполните Клонирование репозитория концентраторов событий Azure для Kafka `flink` и перейдите к вложенной папке:

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

Теперь производитель начнет отправлять события в концентратор событий в разделе `test` и печатать события в stdout.

## <a name="run-flink-consumer"></a>Запуск потребителя Flink

Используя предоставленный пример потребителя, вы получаете сообщения из концентратора событий. 

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

Если в концентраторе событий есть события (например, если производитель также работает), потребитель теперь начинает получать события из раздела `test`.

Дополнительные сведения о подключении Flink к Kafka см. в [руководстве по соединителю Kafka Flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html).

## <a name="next-steps"></a>Дальнейшие шаги
Дополнительные сведения о концентраторах событий для Kafka см. в следующих статьях:  

- [Зеркальное отображение брокера Kafka в концентраторе событий](event-hubs-kafka-mirror-maker-tutorial.md)
- [Подключение Apache Spark к концентратору событий](event-hubs-kafka-spark-tutorial.md)
- [Интеграция Kafka Connect с концентратором событий](event-hubs-kafka-connect-tutorial.md)
- [Migrating to Azure Event Hubs for Apache Kafka Ecosystems](https://github.com/Azure/azure-event-hubs-for-kafka) (Переход в Центры событий Azure для экосистем Apache Kafka)
- [Подключение Akka Streams к концентратору событий](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka Guide для разработчиков концентраторов событий Azure](apache-kafka-developer-guide.md)