---
title: Устранение распространенных ошибок в Azure Cosmos DB API Cassandra
description: В этом документе обсуждаются способы устранения распространенных проблем, возникающих в Azure Cosmos DB API Cassandra
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: f5f2cb5ac8c354df38310cdcb47b98e1da5b6cfa
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97521841"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Устранение распространенных неполадок в Azure Cosmos DB API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API Cassandra в Azure Cosmos DB — это уровень совместимости, который обеспечивает [поддержку протокола проводного](cassandra-support.md) подключения для популярной базы данных Apache Cassandra с открытым кодом и работает с [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction). В качестве полностью управляемой облачной службы Azure Cosmos DB предоставляет [гарантии доступности, пропускной способности и согласованности](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) для API Cassandra. Эти гарантии невозможно реализовать в устаревших реализациях Apache Cassandra. API Cassandra также упрощает операции с платформой нулевого обслуживания и исправление без простоя. Таким образом, многие из внутренних серверных операций отличаются от Apache Cassandra, поэтому мы рекомендуем использовать определенные параметры и подходы, чтобы избежать распространенных ошибок. 

В этой статье описаны распространенные ошибки и решения для приложений, использующих Azure Cosmos DB API Cassandra.

## <a name="common-errors-and-solutions"></a>Распространенные ошибки и способы их устранения

| Ошибка               |  Описание             | Решение  |
|---------------------|--------------------------|-----------|
| Оверлоадедексцептион (Java) | Общее число потребляемых единиц запросов превышает количество единиц запросов, подготовленных для пространства ключей или таблицы. Поэтому запросы будут регулироваться. | Рассмотрите возможность масштабирования пропускной способности, назначенной пространства ключей или таблице, из портал Azure (см. [здесь](manage-scale-cassandra.md) для операций масштабирования в API Cassandra) или можно реализовать политику повтора. Сведения об Java см. в статье примеры повторных попыток для драйвера [v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) и [драйвера v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). См. также [расширения Azure Cosmos Cassandra для Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) |
| Оверлоадедексцептион (Java) даже с достаточной пропускной способностью | Система выглядит как запросы регулирования, несмотря на достаточную пропускную способность для объема запросов и/или потребляемую стоимость единицы запроса.  | API Cassandra реализует бюджет пропускной способности системы для операций на уровне схемы (CREATE TABLE, ALTER TABLE, DROP TABLE). Этот бюджет должен быть достаточно для операций с схемами в рабочей системе. Однако при наличии большого количества операций на уровне схемы возможно превышение этого предела. Так как этот бюджет не контролируется пользователями, необходимо рассмотреть возможность снижения числа выполняемых операций с схемой. Если это действие не устраняет проблему или не является целесообразным для вашей рабочей нагрузки, [Создайте запрос в службу поддержки Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).|
| Клоседконнектионексцептион (Java) | По истечении периода бездействия при успешном подключении приложение не сможет подключиться.| Эта ошибка может быть вызвана временем ожидания простоя Azure LoadBalancers, что составляет 4 минуты. Установите параметр проверки активности в разделе драйвер (см. ниже) и увеличьте параметры поддержания активности в операционной системе или [Настройте время ожидания простоя в Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). |
| Другие ошибки периодического подключения (Java) | Неожиданное завершение подключения или время ожидания | Драйверы Apache Cassandra для Java предоставляют две встроенные политики переподключения: `ExponentialReconnectionPolicy` и `ConstantReconnectionPolicy` . Значение по умолчанию — `ExponentialReconnectionPolicy`. Однако для Azure Cosmos DB API Cassandra рекомендуется использовать `ConstantReconnectionPolicy` задержку в 2 секунды. См. [документацию по драйверу](https://docs.datastax.com/developer/java-driver/4.9/manual/core/reconnection/)  для Java v4. x [и рекомендации по использованию](https://docs.datastax.com/developer/java-driver/3.7/manual/reconnection/) Java 3. x (см. также примеры ниже).|

Если ваша ошибка не указана выше и возникла ошибка при выполнении [поддерживаемой операции в API Cassandra](cassandra-support.md), где ошибка отсутствует *при использовании собственного Apache Cassandra*, [Создайте запрос в службу поддержки Azure](../azure-portal/supportability/how-to-create-azure-support-request.md) .

## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Настройка драйвера Реконнектионполици для Java

### <a name="version-3x"></a>Версия 3.x

Для версии 3. x драйвера Java настройте политику повторного подключения при создании объекта кластера.

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Версия 4. x

Для версии 4. x драйвера Java настройте политику повторного подключения, переопределив параметры в `reference.conf` файле:

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-java-driver"></a>Включение проверки активности для драйвера Java

### <a name="version-3x"></a>Версия 3.x

Для версии 3. x драйвера Java установите флажок "оставаться в активном состоянии" при создании объекта кластера и убедитесь, что [в операционной системе включен](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)параметр проверки активности.

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>Версия 4. x

Для версии 4. x драйвера Java установите параметр "оставаться в активном состоянии", переопределив параметры в `reference.conf` и установив [флажок "Проверка активности" в операционной системе](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089).

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Дальнейшие шаги

- Сведения о [поддерживаемых функциях](cassandra-support.md) в Azure Cosmos DB API Cassandra.
- Узнайте, как [перейти с машинного кода Apache Cassandra на Azure Cosmos DB API Cassandra](cassandra-migrate-cosmos-db-databricks.md)

