---
title: Руководство разработчика Apache Kafka для концентраторов событий
description: В этой статье содержатся ссылки на статьи, описывающие, как интегрировать приложения Kafka с концентраторами azure Event.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: b4d9dc51451a06b2c87ddce78547655b51c6fabd
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729630"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Руководство разработчиков Apache Kafka для центров событий Azure
В этой статье содержатся ссылки на статьи, описывающие, как интегрировать приложения Apache Kafka с концентраторами событий Azure. 

## <a name="overview"></a>Обзор
Центры событий предоставляют конечную точку Kafka, которую существующие приложения на основе Kafka могут использовать в качестве альтернативы запуска собственного кластера Kafka. Центры событий поддерживают Apache Kafka 1.0 и более новые версии протокола и работают с имеющимися приложениями Kafka, в том числе с MirrorMaker. Для получения дополнительной информации см. [Концентраторы событий для Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Краткие руководства
Вы можете найти quickstarts в GitHub и в этом наборе содержимого, который поможет вам быстро нарастить на события концентраторов для Kafka.

### <a name="quickstarts-in-github"></a>Быстрые запуски в GitHub
Смотрите следующие quickstarts в **лазурном событии-хаб-для-кафки** репо: 

| Язык/рамочная структура клиента | Описание | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Этот быстрый запуск покажет, как создавать и подключаться к конечная точка событий Концентраторы Kafka с помощью примера производителя и потребителя, написанного в C s с помощью .NET Core 2.0.</p><p>Этот пример основан на [клиенте Confluent Apache Kafka .NET,](https://github.com/confluentinc/confluent-kafka-dotnet)измененном для использования с концентраторами событий для Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Этот быстрый запуск покажет, как создавать и подключаться к конечная точка событий Концентраторы Kafka с помощью примера производителя и потребителя, написанного на Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Этот быстрый запуск покажет, как создавать и подключаться к конечная точка событий Концентраторка Кафка с помощью примера производителя и потребителя, написанного в узле.</p><p>В этом примере используется библиотека [узлов-rdkafka.](https://github.com/Blizzard/node-rdkafka) </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Этот quickstart покажет, как создавать и подключаться к конечная точка событий кцентров Kafka с помощью примера производителя и потребителя, написанного в питоне.</p><p>Этот пример основан на [клиенте Confluent Apache Kafka Python,](https://github.com/confluentinc/confluent-kafka-python)измененном для использования с концентраторами событий для Kafka.</p>|
| [GO](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Этот quickstart покажет, как создавать и подключаться к конечная точка событий концентратор кафки с помощью примера производителя и потребителя, написанного в Go.</p><p>Этот пример основан на [клиенте Confluent Apache Kafka Golang,](https://github.com/confluentinc/confluent-kafka-go)измененном для использования в Event Hubs для Kafka.</p>| 
| [Сарама Кафка Го](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Этот quickstart покажет, как создавать и подключаться к конечная точка событий концентратор кафки с помощью примера производителя и потребителя, написанного в Go с помощью библиотеки [клиентов Sarama Kafka.](https://github.com/Shopify/sarama) |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Этот быстрый запуск покажет, как создавать и подключаться к конечная точка событий Концентраторы Кафки с помощью CLI, который поставляется в комплекте с распределением Apache Kafka.| 
| [Кафкакат](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat является не-JVM командной линии потребителей и производителей на основе librdkafka, популярный из-за его скорости и небольшой след. Этот quickstart содержит образец конфигурации и несколько простых команд kafkacat образца. | 
 
### <a name="quickstarts-in-docs"></a>Быстрые запуски в DOCS
Смотрите быстрый запуск: [потоковая передача данных с помощью концентраторов событий с помощью протокола Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) в этом наборе содержимого, который предоставляет пошаговые инструкции о том, как потоквать в концентраторы событий. Вы узнаете, как использовать ваши производители и потребители, чтобы поговорить с Event Hubs только с изменением конфигурации в приложениях. 


## <a name="tutorials"></a>Учебники 

### <a name="tutorials-in-github"></a>Учебники в GitHub
Смотрите следующие учебники по GitHub:

| Учебник | Описание | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | В этом уроке показано, как подключить Потоки Акка к концентрам событий с поддержкой Kafka без изменения клиентских протоколов или запуска собственных кластеров. Есть два отдельных учебника, использующих языки программирования **Java** и **Scala.** | 
| [Подключить](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Этот документ поможет вам интегрировать Kafka Connect с azure Event Hubs и развернуть основные разъемы FileStreamSource и FileStreamSink. Хотя эти разъемы не предназначены для использования в производстве, они демонстрируют сквозной сценарий Kafka Connect, в котором Azure Event Hubs маскируется под брокера Kafka.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Этот документ будет проходить через интеграцию Filebeat и событий концентраторов через Filebeat в Кафка вывода. | 
| [Флинк](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | В этом уроке будет уотена возможность подключения Apache Flink к концентратам событий с поддержкой Kafka без изменения клиентских протоколов или запуска собственных кластеров. | 
| [Свободноd](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Этот документ будет проходить через интеграцию Fluentd `out_kafka` и событий концентраторов с помощью выходного плагина для Fluentd. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | В этом учебнике показано, как обмениваться событиями между потребителями и производителями с помощью различных протоколов. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Этот учебник будет ходить вам через интеграцию Logstash с Кафка с поддержкой событий концентраторов с помощью Logstash Kafka ввода / вывода плагинов. | 
| [Зеркальный лавка](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | В этом учебнике показано, как концентратор событий и Kafka MirrorMaker могут интегрировать существующий конвейер Kafka в Azure, отражая поток ввода Kafka в службе концентраторов событий. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | В этом уроке будет уотена возможность подключения Apache NiFi к пространству имен Event Hubs. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Быстрые запуски покажут, как создавать и подключаться к конечная точка событий Концентраторы Kafka с помощью примера производителя и потребителя, написанного на языках программирования Go и Java. |
| [Реестр схемы конфлюкса](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Этот учебник проведет вас через интеграцию Схемы реестра и событий концентраторов для Кафки. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | В этом уроке будет уотена возможность подключения приложения Spark к центру событий без изменения клиентских протоколов или запуска собственных кластеров Kafka. | 

### <a name="tutorials-in-docs"></a>Учебники в DOCS
Кроме того, см. учебник: [Процесс Apache Kafka для событий событий концентраторов событий с использованием stream Analytics](event-hubs-kafka-stream-analytics.md) в этом наборе содержимого, который показывает, как передавать данные в концентраторы событий и обрабатывать их с помощью Azure Stream Analytics.

## <a name="how-to-guides"></a>Практические руководства
Смотрите следующие руководства how-to в нашей документации:

| Статья | Описание | 
| ------- | ----------- | 
| [Зеркальное отображение брокера Kafka в концентраторе событий](event-hubs-kafka-mirror-maker-tutorial.md) | Показывает, как отразить брокера Kafka в центре событий с помощью Kafka MirrorMaker. |
| [Подключение Apache Spark к концентратору событий](event-hubs-kafka-spark-tutorial.md) | Вас подключает к приложению Spark к концентрам событий для потоковой передачи в режиме реального времени. |
| [Подключение Apache Flink к концентратору событий](event-hubs-kafka-flink-tutorial.md) | Показывает, как подключить Apache Flink к центру событий без изменения клиентских протоколов или запуска собственных кластеров. |
| [Интеграция Apache Kafka Connect с центром событий (Предварительный просмотр)](event-hubs-kafka-connect-tutorial.md) | Прогулки вас через интеграцию Kafka Connect с концентратором событий и развертывание основных разъемов FileStreamSource и FileStreamSink. |
| [Подключение Akka Streams к концентратору событий](event-hubs-kafka-akka-streams-tutorial.md) | Показывает, как подключить Akka Streams к концентратору событий, не меняя клиентов протокола и не запуская собственные кластеры. |
| [Используйте стартер Spring Boot для Apache Kafka с концентраторами событий Azure](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Демонстрирует, как настроить на основе Java Весенний облачный поток Binder, созданный с помощью Инициала Весной загрузки, чтобы использовать Apache Kafka с azure Event Hubs. |

## <a name="next-steps"></a>Следующие шаги
Просмотрите образцы в GitHub репо [лазурно-событие-hubs-для-кафки](https://github.com/Azure/azure-event-hubs-for-kafka) под quickstart и учебники папки.

Кроме того, см.

- [Apache Кафка руководство по устранению неполадок для событий концентраторов](apache-kafka-troubleshooting-guide.md)
- [Часто задаваемые вопросы - Концентраторы событий для Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Руководство по миграции Apache Kafka для центров событий](apache-kafka-migration-guide.md)



