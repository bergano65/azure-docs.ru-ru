---
title: Использование AMQP с API службы сообщений Java & служебной шины Azure
description: Как использовать службу сообщений Java (JMS) со Служебной шиной Azure и Расширенным протоколом управления очередью сообщений (AMQP) 1.0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119163"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Использование службы сообщений Java (JMS) с служебной шиной Azure и AMQP 1,0

Поддержка протокола **Расширенный протокол управления очередью сообщений (AMQP) 1,0** в служебной шине Azure означает, что вы можете использовать возможности обмена сообщениями в очереди и публикации/подписки через посредника из различных платформ с помощью эффективного двоичного протокола. Кроме того, можно создавать приложения, содержащие компоненты, созданные с использованием разнообразных языков, платформ и операционных систем.

В этой статье объясняется, как использовать функции обмена сообщениями служебной шины Azure (очереди и разделы публикации и подписки) из приложений Java с помощью популярного API **службы сообщений Java (JMS)** по протоколу AMQP.

## <a name="get-started-with-service-bus"></a>Приступая к работе со служебной шиной
В этом учебнике предполагается, что у вас уже есть пространство имен служебной шины, содержащее очередь с именем `basicqueue` . Если этого не сделать, то можно [создать пространство имен и очередь](service-bus-create-namespace-portal.md) с помощью [портал Azure](https://portal.azure.com). Дополнительные сведения о создании пространства имен и очередей служебной шины см. в статье [Приступая к работе с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Секционированные очереди и разделы также поддерживают AMQP. Дополнительные сведения см. в статьях [Секционированные сущности обмена сообщениями](service-bus-partitioning.md) и [Поддержка AMQP 1.0 для секционированных очередей и разделов служебной шины](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
>

## <a name="what-jms-features-are-supported"></a>Какие функции JMS поддерживаются?

Ниже приведены функции JMS, которые поддерживаются в служебной шине Azure.

| Компоненты | Стандартный уровень служебной шины Azure — JMS 1,1 | Уровень Premium служебной шины Azure — JMS 2,0 (Предварительная версия) |
|---|---|---|
| Автоматическое создание сущностей через AMQP | Не поддерживается | **Поддерживается** |
| Очереди | **Поддерживается** | **Поддерживается** |
| Разделы | **Поддерживается** | **Поддерживается** |
| Временные очереди | Не поддерживается <br/> (Вместо этого создайте обычную очередь с набором *AutoDeleteOnIdle* .) | **Поддерживается** |
| Временные разделы | Не поддерживается | **Поддерживается** |
| Селекторы сообщений | Не поддерживается | **Поддерживается** |
| Браузеры очередей | Не поддерживается <br/> (Используйте функцию " *Просмотр* " API служебной шины). | **Поддерживается** |
| Общие устойчивые подписки | **Поддерживается** | **Поддерживается**|
| Несовместное использование устойчивых подписок | Не поддерживается | **Поддерживается** |
| Общие неустойчивые подписки | Не поддерживается | **Поддерживается** |
| Неустойчивые подписки без общих ресурсов | Не поддерживается | **Поддерживается** |
| Отмена подписки на устойчивые подписки | Не поддерживается | **Поддерживается** |
| рецеивеноваит | Не поддерживается | **Поддерживается** |
| Распределенные транзакции | Не поддерживается | Не поддерживается |
| Устойчивый якоря | Не поддерживается | Не поддерживается |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>Дополнительные предостережения для уровня "Стандартный" служебной шины
В каждом **сеансе**допускается только один **MessageProducer** или **MessageConsumer** . Если требуется создать несколько **MessageProducers** или **MessageConsumers** в приложении, создайте специальный **сеанс** для каждого из них.

## <a name="downloading-the-java-message-service-jms-client-library"></a>Загрузка клиентской библиотеки службы сообщений Java (JMS)

Чтобы подключиться к служебной шине Azure и использовать API-интерфейс (JMS) службы сообщений Java через AMQP, необходимо использовать приведенные ниже библиотеки. Их необходимо добавить в путь сборки с помощью предпочтительного средства управления зависимостями для вашего проекта.

Необходимая клиентская библиотека зависит от используемой ценовой категории.

### <a name="premium-tier---jms-20-over-amqp-preview"></a>Уровень Premium — JMS 2,0 over AMQP (Предварительная версия)

Чтобы использовать все функции предварительной версии, доступные на уровне Premium служебной шины Azure, используйте библиотеку [Azure-servicebus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) .

### <a name="standard-tier---jms-11-over-amqp"></a>Уровень Standard — JMS 1,1 over AMQP

Для использования функций JMS, поддерживаемых стандартным уровнем служебной шины (см [. раздел какие функции JMS поддерживаются?](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)) используйте указанные ниже библиотеки.

* [Спецификация Жеронимо JMS 1,1](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Клиент Qpid JMS](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> Имена и версии JMS JAR могли измениться. Дополнительные сведения см. в разделе [Qpid JMS — AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).
>

## <a name="coding-java-applications"></a>Создание приложений Java

После импорта зависимостей приложения Java могут быть написаны независимым образом с помощью поставщика JMS.

Так как служебная шина Azure уровня "Стандартный" и "Премиум" отличаются зависимостями и числом поддерживаемых ими функций JMS, модель программирования немного отличается.

> [!IMPORTANT]
> Приведенное ниже руководство демонстрирует, как подключиться к служебной шине Azure с помощью простого приложения.
>
> Учитывая, что большинство архитектур корпоративных приложений могут иметь настраиваемый способ управления зависимостями и конфигурациями, используйте приведенную ниже ссылку, чтобы понять, что нужно и адаптировать к приложению соответствующим образом.
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>Подключение к служебной шине Azure с помощью JMS

Для подключения к служебной шине Azure с помощью клиентов JMS требуется **строка** подключения, доступная в разделе "политики общего доступа" в [портал Azure](https://portal.azure.com) в **строке основной строки соединения**.


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>Подключение к Premium служебной шины Azure через JMS 2,0 (Предварительная версия)

1. Создайте экземпляр`ServiceBusJmsConnectionFactorySettings`
    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();

    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Создайте экземпляр `ServiceBusJmsConnectionFactory` с соответствующим `ServiceBusConnectionString` .
    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Используйте `ConnectionFactory` для создания, `Connection` а затем`Session` 
    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    или `JMSContext` (для клиентов JMS 2,0)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>Подключение к стандарту служебной шины Azure через JMS 1,1

1. Вставьте конфигурацию служебной шины Azure в файл свойств JNDI с именем **servicebus. Properties**.
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. Настройка контекста JNDI и настройка ConnectionFactory
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. Используйте `ConnectionFactory` для создания, `Connection` а затем a `Session` .
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>Написание приложения JMS

После `Session` `JMSContext` создания экземпляра или, приложение может использовать знакомые API-интерфейсы JMS для выполнения операций управления и работы с данными.

Сведения о том, какие интерфейсы API будут поддерживаться на каждом уровне, см. в списке [поддерживаемых функций JMS](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported) для уровня "Стандартный" и "Премиум".

## <a name="summary"></a>Сводка
В этом руководство показано, как клиентские приложения Java, использующие службу сообщений Java (JMS) через AMQP 1,0, могут взаимодействовать с служебной шиной Azure.

Протокол AMQP 1.0 Service Bus можно также использовать из других языков, в числе которых .NET, C, Python и PHP. Компоненты, созданные с помощью этих языков, могут надежно и точно обмениваться сообщениями, используя AMQP 1.0 в Service Bus.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о служебной шине Azure и сведения о сущностях службы сообщений Java (JMS) см. по ссылкам ниже. 
* [Служебная шина — очереди, разделы и подписки](service-bus-queues-topics-subscriptions.md)
* [Служебная шина — сущности службы сообщений Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Поддержка AMQP 1.0 в служебной шине](service-bus-amqp-overview.md)
* [Инструкции для разработчиков служебной шины AMQP 1,0](service-bus-amqp-dotnet.md)
* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)

