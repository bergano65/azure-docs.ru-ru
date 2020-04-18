---
title: Мигрировать в концентраторы событий Azure для Apache Kafka
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
ms.openlocfilehash: 806a1f16327ad72a7f3527c813b355e1ba807dda
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606760"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Мигрируйте в центры событий Azure для экосистем Apache Kafka
Azure Event Hubs предоставляет конечную точку Apache Kafka, которая позволяет подключиться к кцентратам событий с помощью протокола Kafka. Внося минимальные изменения в существующее приложение Kafka, вы можете подключиться к кцентратам azure Event и воспользоваться преимуществами экосистемы Azure. Концентраторы событий для Kafka поддерживают [версию Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) и позже.

## <a name="pre-migration"></a>Подготовка к миграции 

### <a name="create-an-azure-account"></a>Создание учетной записи Azure
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

### <a name="create-an-event-hubs-namespace"></a>Создание пространства имен в Центрах событий
Следуйте пошаговым инструкциям в статье [«Создание концентратора событий»](event-hubs-create.md) для создания пространства имен событий и концентратора событий. 

### <a name="connection-string"></a>Строка подключения
Выполните шаги из [строки подключения Get из статьи портала.](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) И, запишите вниз строку соединения для более последующего использования. 

### <a name="fully-qualified-domain-name-fqdn"></a>Полностью квалифицированное доменное имя (ФЗДН)
Вам также может понадобиться F-DN, который указывает на пространство имен Концентратора событий. В строке соединения можно найти строку F'DN следующим образом:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Если пространство имен Event Hubs развернуто в непубличном облаке, ваше \*доменное \*имя может \*отличаться (например, .servicebus.chinacloudapi.cn, .servicebus.usgovcloudapi.net или .servicebus.cloudapi.de).

## <a name="migration"></a>Миграция 

### <a name="update-your-kafka-client-configuration"></a>Обновление конфигурации клиента Kafka

Для подключения к концентратору событий с поддержкой Kafka необходимо обновить конфигурацию клиентов Kafka. Если у вас возникли проблемы с `bootstrap.servers` поиском вашего, попробуйте найти, где установлен в вашем приложении.

Вставьте следующие конфигурации, где имеет смысл в вашем приложении. Убедитесь в `bootstrap.servers` том, чтобы обновить и `sasl.jaas.config` значения, чтобы направить клиента к вашей конечной точке событий концентратор Кафка с правильной аутентификацией. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Если `sasl.jaas.config` в вашей инфраструктуре не поддерживается конфигурация, найдите конфигурации, которые используются для установки имени пользователя и пароля SASL, и используйте их вместо этого. Установите имя `$ConnectionString` пользователя и пароль к строке подключения Концентраторов событий.

## <a name="post-migration"></a>Действия после миграции
Запустите приложение Kafka, отправляещее события в концентратор событий. Затем убедитесь, что концентратор событий получает события с помощью портала Azure. На странице **«Обзор»** в пространстве имен событий —сямнитесь в представление **Сообщений** в разделе **Метрики.** Обновите страницу, чтобы обновить диаграмму. На отображение полученных сообщений может уйти несколько секунд. 

[![Проверка получения сообщения концентратором событий](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о Центрах событий и Центрах событий для Kafka см. в следующих статьях:  

- [Apache Кафка руководство по устранению неполадок для событий концентраторов](apache-kafka-troubleshooting-guide.md)
- [Часто задаваемые вопросы - Концентраторы событий для Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Руководство разработчиков Apache Kafka для центров событий Azure](apache-kafka-developer-guide.md)
- [Рекомендуемые конфигурации](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
- 