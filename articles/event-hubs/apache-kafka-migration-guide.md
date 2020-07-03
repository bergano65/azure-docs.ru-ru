---
title: Перенос в концентраторы событий Azure для Apache Kafka
description: В этой статье показано, как через Центры событий Azure организовать обмен событиями между потребителями и производителями, которые используют разные протоколы (AMQP, Apache Kafka и HTTPS).
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 32b08e565b86af8f6373c9848211646128bb346d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81677354"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Переход на концентраторы событий Azure для экосистем Apache Kafka
Концентраторы событий Azure предоставляют конечную точку Apache Kafka, которая позволяет подключаться к концентраторам событий с помощью протокола Kafka. Внося минимальные изменения в существующее приложение Kafka, вы можете подключиться к концентраторам событий Azure и воспользоваться преимуществами экосистемы Azure. Концентраторы событий для Kafka поддерживают [Apache Kafka версии 1,0](https://kafka.apache.org/10/documentation.html) и более поздних.

## <a name="pre-migration"></a>Подготовка к миграции 

### <a name="create-an-azure-account"></a>Создание учетной записи Azure
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

### <a name="create-an-event-hubs-namespace"></a>Создание пространства имен в Центрах событий
Выполните пошаговые инструкции из статьи [Создание концентратора событий](event-hubs-create.md) , чтобы создать пространство имен концентраторов событий и концентратор событий. 

### <a name="connection-string"></a>Строка подключения.
Выполните действия из [строки "получение подключения" из статьи на портале](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) . Запишите строку подключения для последующего использования. 

### <a name="fully-qualified-domain-name-fqdn"></a>Полное доменное имя (FQDN)
Вам также может потребоваться полное доменное имя, указывающее на пространство имен концентратора событий. Полное доменное имя можно найти в строке подключения следующим образом:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Если пространство имен концентраторов событий развернуто в общедоступном облаке, имя домена может отличаться (например, \*. servicebus.chinacloudapi.CN, \*. servicebus.usgovcloudapi.NET или \*. servicebus.cloudapi.de).

## <a name="migration"></a>Миграция 

### <a name="update-your-kafka-client-configuration"></a>Обновление конфигурации клиента Kafka

Чтобы подключиться к концентратору событий с поддержкой Kafka, необходимо обновить конфигурации клиента Kafka. Если у вас возникли проблемы с поиском, попробуйте найти `bootstrap.servers` место, заданное в приложении.

Вставьте следующие конфигурации в любом месте, где это имеет смысл в приложении. Обязательно обновите значения `bootstrap.servers` и `sasl.jaas.config` , чтобы направить клиент в конечную точку Kafka концентраторов событий с правильной проверкой подлинности. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Если `sasl.jaas.config` конфигурация не поддерживается в вашей платформе, найдите конфигурации, используемые для задания имени пользователя и пароля SASL и используйте их. Задайте для `$ConnectionString` параметра имя пользователя значение и пароль для строки подключения к концентратору событий.

## <a name="post-migration"></a>После миграции
Запустите приложение Kafka, которое отправляет события в концентратор событий. Затем убедитесь, что концентратор событий получает события с помощью портал Azure. На странице **Обзор** пространства имен концентраторов событий перейдите в представление **сообщения** в разделе **метрики** . Обновите страницу, чтобы обновить диаграмму. На отображение полученных сообщений может уйти несколько секунд. 

[![Проверка получения сообщения концентратором событий](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Дальнейшие шаги
Дополнительные сведения о Центрах событий и Центрах событий для Kafka см. в следующих статьях:  

- [Руководство по устранению неполадок Apache Kafka для концентраторов событий](apache-kafka-troubleshooting-guide.md)
- [Часто задаваемые вопросы — концентраторы событий для Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Apache Kafka Guide для разработчиков концентраторов событий Azure](apache-kafka-developer-guide.md)
- [Рекомендуемые конфигурации](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)