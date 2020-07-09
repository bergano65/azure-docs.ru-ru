---
title: Перенос приложений службы сообщений Java (JMS) из Активемк в служебную шину Azure | Документация Майкрософт
description: В этой статье объясняется, как перенести существующие приложения JMS, взаимодействующие с Active MQ, для взаимодействия с служебной шиной Azure.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122413"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>Перенос существующих приложений службы сообщений Java (JMS) 2,0 из Active MQ в служебную шину Azure

Служебная шина Azure поддерживает Java/J2EE и пружинные рабочие нагрузки, в которых используется API-интерфейс JMS 2,0 через протокол расширенного протокола очередей сообщений (AMQP).

В этом руководством описывается, что следует учитывать при необходимости изменить существующее приложение службы сообщений Java (JMS) 2,0, которое взаимодействует с брокером JMS (в частности Apache Активемк или Amazon MQ) для взаимодействия с служебной шиной Azure.

## <a name="before-you-start"></a>Перед началом работы

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Различия между служебной шиной Azure и Apache Активемк

Служебная шина Azure и Apache Активемк — это оба брокера, которые работают как поставщики JMS, чтобы клиентские приложения отправляли и получали сообщения. Они включают семантику "точка-точка" с **очередями** и семантику публикации и подписки с **разделами** и **подписками**. 

Несмотря на это, существуют некоторые различия между ними.

| Категория | Активный MQ | Служебная шина Azure |
| --- | --- | --- |
| Распределение по уровням приложений | Кластеризованный Монолит | Двухуровневая <br> (Шлюз + Серверная часть) |
| Поддержка протоколов | <ul> <li>AMQP</li> <li> стомп </li> <li> опенвире </li> </ul> | AMQP |
| Режим подготовки | <ul> <li> IaaS (локально) </li> <li> Amazon MQ (управляемая PaaS) </li> | Управляемая PaaS |
| Размер сообщения | Настраиваемый пользователем | 1 МБ (уровень Premium) |
| Высокий уровень доступности | Организуется пользователем | Управляемая платформа |
| Аварийное восстановление | Организуется пользователем | Управляемая платформа | 

### <a name="current-supported-and-unsupported-features"></a>Текущие поддерживаемые и неподдерживаемые функции

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>Предупреждения

Двухэтапный уровень служебной шины Azure предоставляет различные возможности обеспечения непрерывности бизнес-процессов (высокий уровень доступности и аварийное восстановление). Однако при использовании функций JMS необходимо учитывать некоторые моменты.

#### <a name="service-upgrades"></a>Обновления службы

В случае обновления и перезапуска служебной шины временные очереди или разделы будут удалены.

Если приложение чувствительно к утере данных во временных очередях или разделах, рекомендуется **не** использовать временные очереди или разделы и использовать устойчивые очереди, разделы и подписки.

#### <a name="data-migration"></a>Перенос данных

В рамках миграции и изменения клиентских приложений для взаимодействия с служебной шиной Azure данные, хранящиеся в Активемк, не будут перенесены в служебную шину.

Для очистки очередей, разделов и подписок Активемк и воспроизведения сообщений в очередях, разделах и подписках служебной шины может потребоваться пользовательское приложение.

#### <a name="authentication-and-authorization"></a>Аутентификация и авторизация

Управление доступом на основе ролей (RBAC), поддерживаемое Azure ActiveDirectory, является предпочтительным механизмом проверки подлинности для служебной шины Azure.

Однако, поскольку RBAC в настоящее время не поддерживается из-за отсутствия поддержки проверки подлинности на основе утверждений Apache QPID JMS.

Сейчас проверка подлинности поддерживается только с ключами SAS.

## <a name="pre-migration"></a>Подготовка к миграции

### <a name="version-check"></a>Проверка версии

Ниже приведены компоненты, используемые при записи приложений JMS и поддерживаемых версий. 

| Компоненты | Версия |
|---|---|
| API-интерфейс службы сообщений Java (JMS) | 1,1 или выше |
| Протокол AMQP | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Убедитесь, что порты AMQP открыты

Служебная шина Azure поддерживает обмен данными по протоколу AMQP. Для этой цели необходимо включить связь через порты 5671 (AMQP) и 443 (TCP). В зависимости от того, где размещаются клиентские приложения, может потребоваться обращение в службу поддержки, чтобы разрешить обмен данными через эти порты.

> [!IMPORTANT]
> Служебная шина Azure поддерживает **только** протокол AMQP 1,0.

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>Настройка корпоративных конфигураций (виртуальная сеть, брандмауэр, Частная конечная точка и т. д.)

Служебная шина Azure обеспечивает различные функции безопасности и обеспечения высокого уровня доступности. Для получения дополнительных сведений см. следующие ссылки на документацию.

  * [Конечные точки службы виртуальной сети](service-bus-service-endpoints.md)
  * [Брандмауэр](service-bus-ip-filtering.md).
  * [Шифрование на стороне службы с помощью управляемого клиентом ключа (BYOK)](configure-customer-managed-key.md)
  * [Частные конечные точки](private-link-service.md)
  * [Проверка подлинности и авторизация](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Мониторинг, оповещения и трассировка

Метрики публикуются для каждого пространства имен служебной шины на Azure Monitor и могут использоваться для оповещения и динамического масштабирования ресурсов, выделенных для пространства имен.

Дополнительные сведения о различных метриках и о настройке оповещений см. в статье [метрики служебной шины в Azure Monitor](service-bus-metrics-azure-monitor.md).

Кроме того, ознакомьтесь с дополнительными сведениями о [трассировке на стороне клиента для операций с данными](service-bus-end-to-end-tracing.md) , а также [операционными и диагностическими операциями управления](service-bus-diagnostic-logs.md)

### <a name="metrics---newrelic"></a>Метрики — NewRelic

Ниже приведено удобное средство, в котором метрики из Активемк сопоставлены с метриками в служебной шине Azure. Ниже приведены ссылки из NewRelic.

  * [Метрики NewRelic Активемк/Amazon MQ](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Метрики NewRelic служебной шины Azure](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> В настоящее время NewRelic не имеет прямой интеграции с Активемк напрямую, но у них есть метрики, доступные для Amazon MQ.
> Так как Amazon MQ является производным от Активемк, в приведенном ниже разделе описано, как сопоставить метрики NewRelic из Амазонмк с служебной шиной Azure.
>

|Группирование метрик| Метрика Амазонмк/Active MQ | Метрика служебной шины Azure |
|------------|---------------------------|--------------------------|
|Broker|`CpuUtilization`|`CPUXNS`|
|Broker|`MemoryUsage`|`WSXNS`|
|Broker|`CurrentConnectionsCount`|`activeConnections`|
|Broker|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Broker|`InactiveDurableTopicSubscribersCount`|Использование метрик подписки|
|Broker|`TotalMessageCount`|Использование уровня очереди, раздела или подписки`activeMessages`|
|Очередь или раздел|`EnqueueCount`|`incomingMessages`|
|Очередь или раздел|`DequeueCount`|`outgoingMessages`|
|Очередь|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Миграция

Чтобы перенести существующее приложение JMS 2,0 для взаимодействия с служебной шиной Azure, необходимо выполнить приведенные ниже действия.

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>Экспорт топологии из Активемк и создание сущностей в служебной шине Azure (необязательно)

Чтобы обеспечить возможность беспрепятственного подключения клиентских приложений к служебной шине Azure, топологию, которая включает очереди, разделы и подписки, необходимо перенести из **Apache активемк** в **служебную шину Azure**.

> [!NOTE]
> Для приложений службы сообщений Java (JMS) создание очередей, разделов и подписок является операцией времени выполнения. Большинство поставщиков службы сообщений Java (JMS) (брокеры сообщений) предлагают функции создания *очередей*, *разделов* и *подписок* во время выполнения.
>
> Таким образом, приведенный выше шаг является необязательным.
>
> Чтобы обеспечить наличие у приложения разрешений на создание топологии во время выполнения, убедитесь, что используется строка подключения с разрешениями ***"Управление" SAS*** .

Требуемое действие 
  * Использование [программ командной строки активемк](https://activemq.apache.org/activemq-command-line-tools-reference) для экспорта топологии
  * Повторное создание той же топологии с помощью [шаблона Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
  * Выполните шаблон Azure Resource Manager.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Импорт зависимости Maven для реализации JMS служебной шины

Чтобы обеспечить бесперебойное подключение к служебной шине Azure, необходимо добавить пакет ***Azure-servicebus-JMS*** в качестве зависимости в `pom.xml` файл Maven, как показано ниже.

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Изменения конфигурации сервера приложений

Эта часть является настраиваемой для сервера приложений, на котором размещаются клиентские приложения, подключающиеся к Active MQ.

#### <a name="tomcat"></a>Tomcat

Здесь мы начнем с конфигурации, относящейся к активному MQ, как показано в `/META-INF/context.xml` файле.

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

что можно адаптировать ниже для указания на служебную шину Azure

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Пружинные приложения

##### <a name="update-applicationproperties-file"></a>Обновить `application.properties` файл

Если для подключения к Активемк используется приложение с пружинной загрузкой.

Цель состоит в том, чтобы ***Удалить*** из файла свойства, относящиеся к активемк `application.properties` .

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Затем ***добавьте*** определенные свойства служебной шины в `application.properties` файл.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Замена Активемкконнектионфактори на Сервицебусжмсконнектионфактори

Следующим шагом является замена экземпляра Активемкконнектионфактори на Сервицебусжмсконнектионфактори.

> [!NOTE] 
> Фактические изменения кода зависят от приложения и от того, как управляются зависимости, но приведенный ниже пример содержит ***рекомендации по*** изменению.
>

Ранее вы можете создать экземпляр объекта Активемкконнектионфактори, как показано ниже.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Это будет изменено на экземпляр объекта Сервицебусжмсконнектионфактори

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Действия после миграции

Теперь, когда вы изменили приложение, чтобы начать отправку и получение сообщений из служебной шины Azure, следует убедиться, что она работает правильно. После этого можно продолжить и модернизировать стека приложений.

## <a name="next-steps"></a>Дальнейшие действия

Воспользуйтесь [пружинной загрузкой для JMS служебной шины Azure](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) для эффективной интеграции с служебной шиной Azure.

Дополнительные сведения об обмене сообщениями служебной шины и службе сообщений Java (JMS) см. в следующих разделах:

* [JMS служебной шины](service-bus-java-how-to-use-jms-api-amqp.md)
* [Очереди, разделы и подписки служебной шины](service-bus-queues-topics-subscriptions.md)
* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
