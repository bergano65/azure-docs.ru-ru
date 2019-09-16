---
title: Использование AMQP 1,0 с API службы сообщений Java и служебной шиной Azure
description: Как использовать службу сообщений Java (JMS) со Служебной шиной Azure и Расширенным протоколом управления очередью сообщений (AMQP) 1.0.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 03/05/2019
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9dff2cc11b71f314de81fd99ed3b72c6337d977f
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967980"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Использование службы сообщений Java (JMS) с служебной шиной Azure и AMQP 1,0
В этой статье объясняется, как использовать функции обмена сообщениями служебной шины Azure (очереди и разделы публикации и подписки) из приложений Java с помощью популярного стандарта API службы сообщений Java (JMS). Есть [сопутствующая статья](service-bus-amqp-dotnet.md) , в которой объясняется, как сделать то же самое с помощью API .NET служебной шины Azure. Эти два руководства можно использовать совместно для изучения обмена сообщениями между различными платформами с помощью AMQP 1.0.

AMQP 1.0 — это эффективный и надежный протокол обмена сообщениями на уровне соединения, который можно использовать для создания надежных кроссплатформенных приложений для обмена сообщениями.

Поддержка AMQP 1,0 в служебной шине Azure означает, что вы можете использовать возможности обмена сообщениями в очереди и публикации/подписки через посредника из различных платформ с помощью эффективного двоичного протокола. Кроме того, можно создавать приложения, содержащие компоненты, созданные с использованием разнообразных языков, платформ и операционных систем.

## <a name="get-started-with-service-bus"></a>Приступая к работе со служебной шиной
В этом руководстве предполагается, что вы уже создали пространство имен служебной шины, содержащее очередь с именем **basicqueue**. Если это не так, можно [создать пространство имен и очередь](service-bus-create-namespace-portal.md), используя [портал Azure](https://portal.azure.com). Дополнительные сведения о создании пространства имен и очередей служебной шины см. в статье [Приступая к работе с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Секционированные очереди и разделы также поддерживают AMQP. Дополнительные сведения см. в статьях [Секционированные сущности обмена сообщениями](service-bus-partitioning.md) и [Поддержка AMQP 1.0 для секционированных очередей и разделов служебной шины](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Загрузка клиентской библиотеки AMQP 1.0 JMS
Информацию о том, где скачать последнюю версию клиентской библиотеки Apache Qpid JMS AMQP 1.0, см. на странице [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

При построении и запуске приложений JMS с использованием служебной шины необходимо добавить следующие 4 JAR-файла из архива распространения Apache Qpid JMS AMQP 1.0 в Java CLASSPATH:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> Имена и версии JMS JAR могли измениться. Дополнительные сведения см. в разделе [Qpid JMS — AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Создание приложений Java
### <a name="java-naming-and-directory-interface-jndi"></a>Интерфейс JNDI
JMS использует интерфейс JNDI для разделения логических и физических имен. С помощью JNDI разрешаются два типа объектов JMS: ConnectionFactory и Destination. JNDI использует модель поставщика, к которой можно подключить различные службы каталогов для обработки заданий разрешения имен. Библиотека Apache Qpid JMS AMQP 1.0 поставляется с простым файловым поставщиком JNDI, настроенным с помощью файла свойств в следующем формате:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Установка контекста JNDI и настройка ConnectionFactory

Ссылка на **ConnectionString** указывается в "Политике общего доступа" на [портале Azure](https://portal.azure.com) в разделе **Основная строка подключения**
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Настройка производителя и очередей назначения потребителей
Эта запись используется для определения назначения в поставщике JNDI файла свойств Qpid в следующем формате:

Чтобы создать очередь назначения для производителя: 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Чтобы создать очередь назначения для потребителя: 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Написание приложения JMS
Не существует специальных API-интерфейсов или параметров для использования JMS с Service Bus. Однако существует несколько ограничений, которые будут рассмотрены ниже. Как и для любого приложения JMS, первое, что необходимо — это конфигурация среды JNDI, позволяющая разрешать **ConnectionFactory** и назначения.

#### <a name="configure-the-jndi-initialcontext"></a>Настройка исходного контекста JNDI
Для настройки среды JNDI хэш-таблица со сведениями о конфигурации передаются в конструктор класса javax.naming.InitialContext. Два обязательных элемента в хэш-таблице — это имя класса фабрики исходного контекста и URL-адрес поставщика. В следующем примере кода показано, как настроить среду JNDI для использования поставщика JNDI на основе файла свойств Qpid с именем **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Простое приложение JMS, использующее очередь служебной шины
Следующий пример программы отправляет текстовые сообщения JMS в очередь Service Bus с логическим JNDI-именем QUEUE и получает ответные сообщения.

Вы можете получить доступ ко всем исходным кодам и сведениям о конфигурации в [Кратком руководстве. Очередь JMS образцов служебной шины Azure](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Выполнение приложения
Передайте **Строку подключения** из политики совместного доступа для запуска приложения.
Ниже приведены следующие данные запуска приложения.

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Расположение AMQP и сопоставление операции служебной шины
Вот как расположение AMQP преобразовывается в операцию служебной шины.

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Статьи о JMS и Разделы служебной шины
Использование разделов и подписок служебной шины Azure с помощью API-интерфейса службы сообщений Java (JMS) предоставляет базовые возможности отправки и получения. Это удобный вариант при переносе приложений от других брокеров сообщений с помощью API-интерфейсов, совместимых с JMS, даже если разделы служебной шины отличаются от подразделов JMS и требует нескольких корректировок. 

Разделы служебной шины Azure направляют сообщения в именованные, общие и устойчивые подписки, управляемые через интерфейс управления ресурсами Azure, средства командной строки Azure или с помощью портал Azure. Каждая подписка позволяет использовать до 2000 правил выбора, каждое из которых может иметь условие фильтра, а для фильтров SQL — также действие преобразования метаданных. Каждое условие фильтра соответствует входному сообщению, которое должно быть скопировано в подписку техж.  

Получение сообщений из подписок идентично приему сообщений из очередей. У каждой подписки есть связанная очередь недоставленных сообщений, а также возможность автоматически пересылать сообщения в другую очередь или темы. 

Темы JMS позволяют клиентам динамически создавать неустойчивые и устойчивые подписчики, которые при необходимости позволяют фильтровать сообщения с помощью селекторов сообщений. Эти несовместно используемые сущности не поддерживаются служебной шиной. Однако синтаксис правила фильтрации SQL для служебной шины очень похож на синтаксис выбора сообщений, поддерживаемый JMS. 

Сторона издателя раздела JMS совместима с служебной шиной, как показано в этом примере, но динамические подписчики — нет. В служебной шине не поддерживаются следующие API-интерфейсы JMS, связанные с топологией. 

## <a name="unsupported-features-and-restrictions"></a>Неподдерживаемые возможности и ограничения
При использовании JMS по протоколу AMQP 1.0 с Service Bus действуют следующие ограничения:

* Для одного **сеанса** допускается только один **MessageProducer** или **MessageConsumer**. Если требуется создать несколько **MessageProducers** или **MessageConsumers** в приложении, создайте специальный **сеанс** для каждого из них.
* Временные подписки раздела в настоящее время не поддерживаются.
* **MessageSelectors** сейчас не поддерживаются.
* Сеансы транзакций и распределенные транзакции не поддерживаются.

Кроме того, служебная шина Azure разделяет плоскость управления от плоскости данных и, следовательно, не поддерживает несколько функций динамической топологии JMS.

| Неподдерживаемый метод          | Заменить на                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | создать подписки на раздел для переноса селектора сообщений                                 |
| createDurableConsumer       | создать подписки на раздел для переноса селектора сообщений                                 |
| createSharedConsumer        | раздели служебной шины всегда общедоступны (см. выше)                                       |
| createSharedDurableConsumer | раздели служебной шины всегда общедоступны (см. выше)                                       |
| createTemporaryTopic        | создать раздел с помощью API управления, или инструментов, или портала с *AutoDeleteOnIdle*, установленным на срок действия |
| createTopic                 | создать раздел с помощью API управления или инструментов или портала                                           |
| unsubscribe                 | удалить раздел API управления, или инструментов, или портала                                             |
| createBrowser               | не поддерживается. используйте функции Peek () API служебной шины                         |
| createQueue                 | создать очередь с помощью API управления, или инструментов, или портала                                           | 
| createTemporaryQueue        | создать очередь с помощью API управления или инструментов,или портала с *AutoDeleteOnIdle*, установленным на срок действия |
| рецеивеноваит               | Используйте метод Receive (), предоставленный пакетом SDK служебной шины, и укажите очень низкое или нулевое время ожидания. |

## <a name="summary"></a>Сводка
В этом практическом руководстве показано использование функций обмена сообщениями, выполняемых посредством служебной шины (очередей и разделов публикации/подписки), из Java с использованием популярного JMS API и протокола AMQP 1.0.

Протокол AMQP 1.0 Service Bus можно также использовать из других языков, в числе которых .NET, C, Python и PHP. Компоненты, созданные с помощью этих языков, могут надежно и точно обмениваться сообщениями, используя AMQP 1.0 в Service Bus.

## <a name="next-steps"></a>Следующие шаги
* [Поддержка AMQP 1.0 в служебной шине](service-bus-amqp-overview.md)
* [Как использовать AMQP 1.0 с API .NET служебной шины](service-bus-dotnet-advanced-message-queuing.md)
* [Руководство разработчика AMQP 1.0 для служебной шины](service-bus-amqp-dotnet.md)
* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Центр разработчиков Java](https://azure.microsoft.com/develop/java/)

