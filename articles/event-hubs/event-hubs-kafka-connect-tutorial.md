---
title: Интеграция Apache Kafka Connect с Центрами событий Azure | Документация Майкрософт
description: В этой статье рассказывается, как использовать приложение Apache Spark с поддержкой Kafka в Центрах событий Azure.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 2ed4432aec9b833efe6b521b4452177088d21d70
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119417"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>Интеграция поддержки Apache Kafka Connect в Центрах событий Azure (предварительная версия)
По мере увеличения бизнес-потребностей увеличиваются требования по приему из различных внешних источников и приемников. [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) предоставляет такую платформу для подключения, импорта и экспорта данных в любую внешнюю систему или из нее, например MySQL, HDFS и файловую систему, через кластер Kafka. В этом руководстве описано использование платформы Kafka Connect с Центрами событий с поддержкой Kafka.

В этом руководстве описана интеграция Kafka Connect с Центрами событий с поддержкой Kafka и развертывание соединителей FileStreamSource и FileStreamSink. Эта функция в настоящее время находится на стадии предварительной версии. Хотя эти соединители не предназначены для использования в рабочей среде, они показывают комплексный сценарий Kafka Connect, в котором Центры событий Azure действуют в качестве брокера Kafka.

> [!NOTE]
> Этот пример можно найти на сайте [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect).

При работе с этим руководством вы выполните следующие задачи:

> [!div class="checklist"]
> * Создание пространства имен в Центрах событий
> * Клонирование примера проекта
> * Настройка Kafka Connect для Центров событий
> * Выполнение Kafka Connect
> * Создание соединителей

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим пошаговым руководством выполните следующие предварительные требования:

- Подписка Azure. Если ее нет, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/).
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Выпуск Kafka (версии 1.1.1, Scala версии 2.11) доступен на сайте [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Ознакомьтесь со статьей [Центры событий Azure для Apache Kafka (предварительная версия)](https://docs.microsoft.com/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview).

## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен в Центрах событий
Для отправки и получения данных из любой службы Центров событий требуется пространство имен Центров событий. Инструкции о получении конечной точки Kafka концентраторов событий см. в статье [Creating a Kafka enabled Event Hub](event-hubs-create.md) (Создание концентраторов событий с поддержкой Kafka). Получите строку подключения Центров событий и полное доменное имя (FQDN) для последующего использования. Инструкции см. в статье [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Получение строки подключения для Центров событий). 

## <a name="clone-the-example-project"></a>Клонирование примера проекта
Клонируйте репозиторий Центров событий Azure и перейдите к руководствам или вложенной папке подключения: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Настройка Kafka Connect для Центров событий
Минимальная перенастройка необходима при перенаправлении пропускной способности Kafka в Центры событий.  Следующий пример `connect-distributed.properties` показывает, как настроить Connect для проверки подлинности и обмена данных с конечной точкой Kafka в Центрах событий:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

## <a name="run-kafka-connect"></a>Выполнение Kafka Connect

На этом этапе рабочая роль Kafka Connect запускается локально в распределенном режиме с помощью Центров событий для поддержания состояния кластера.

1. Сохраните файл `connect-distributed.properties`, указанный выше, локально.  Не забудьте заменить все значения в фигурных скобках.
2. Перейдите к расположению выпуска Kafka на своем компьютере.
4. Запустите `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties`.  REST API рабочей роли Connect готов к взаимодействию при появлении `'INFO Finished starting connectors and tasks'`. 

> [!NOTE]
> Центры событий поддерживают клиенты Kafka, создавая разделы автоматически. Быстрая проверка пространства имен на портале Azure показывает, что внутренние разделы рабочей роли Connect были созданы автоматически.

### <a name="create-connectors"></a>Создание соединителей
В этом разделе показано использование соединителей FileStreamSource и FileStreamSink. 

1. Создайте каталог для файлов входных и выходных данных.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Создайте два файла. Один файл с начальными данными, которые читает соединитель FileStreamSource, и другой, в который соединитель FileStreamSink выполняет запись.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Создайте соединитель FileStreamSource.  Обязательно замените фигурные скобки своим домашним каталогом.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    Вы должны увидеть `connect-quickstart` Центра событий в экземпляре Центров событий после выполнения предыдущей команды.
4. Проверьте состояние соединителя источника.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    При необходимости, чтобы убедиться, что события были получены из раздела `connect-quickstart`, можно использовать [Explorer служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer/releases).

5. Создайте соединитель FileStreamSink.  Еще раз убедитесь, что вы заменили фигурные скобки своим путем к домашнему каталогу.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Проверьте состояние соединителя приемника.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Убедитесь, что данные реплицированы между файлами и что данные будут одинаковыми для обоих файлов.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Очистка
Kafka Connect создает разделы Центра событий для хранения конфигураций, смещений и состояния, которые сохраняются даже после завершения работы кластера Connect. Если этого не требуется, рекомендуется удалить эти разделы. Вы можете также удалить Центр событий `connect-quickstart`, который был создан при выполнении данного пошагового руководства.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Центрах событий и Центрах событий для Kafka см. в статьях:  

- [Сведения о Центрах событий](event-hubs-what-is-event-hubs.md)
- [Центры событий Azure для Apache Kafka (предварительная версия)](event-hubs-for-kafka-ecosystem-overview.md)
- [Создание концентраторов событий с поддержкой Kafka](event-hubs-create-kafka-enabled.md)
- [Потоковая передача данных в Центры событий из приложений Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Использование Apache Kafka MirrorMaker с Центрами событий](event-hubs-kafka-mirror-maker-tutorial.md)
- [Подключение приложения Apache Spark с помощью Центров событий Azure с поддержкой Kafka](event-hubs-kafka-spark-tutorial.md)
- [Использование Apache Flink с Центрами событий Azure для Apache Kafka](event-hubs-kafka-flink-tutorial.md)
- [Использование Akka Streams с Центрами событий для Apache Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Migrating to Azure Event Hubs for Apache Kafka Ecosystems](https://github.com/Azure/azure-event-hubs-for-kafka) (Переход в Центры событий Azure для экосистем Apache Kafka)