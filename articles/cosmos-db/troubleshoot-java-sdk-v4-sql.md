---
title: Диагностика и устранение неполадок Azure Cosmos DB пакета SDK для Java v4
description: Используйте такие функции, как ведение журнала на стороне клиента и другие сторонние средства для выявления, диагностики и устранения проблем Azure Cosmos DB в пакете SDK для Java версии 4.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/08/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.openlocfilehash: bdec785ccec2c388eb737da3ec494b525941e2a6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982604"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>Устранение неполадок при использовании Azure Cosmos DB пакета SDK версии 4 для Java с учетными записями API SQL

> [!div class="op_single_selector"]
> * [Пакет SDK для Java v4](troubleshoot-java-sdk-v4-sql.md)
> * [Пакет SDK для Async Java версии 2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> В этой статье описывается устранение неполадок только для Azure Cosmos DB пакета SDK для Java версии 4. Дополнительные сведения см. в заметках о выпуске Azure Cosmos DB Java SDK V4, [репозитории Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)и [Советы по повышению производительности](performance-tips-java-sdk-v4-sql.md) . Если в настоящее время используется более старая версия, чем V4, ознакомьтесь с руководством по [переходу на Azure Cosmos DB пакета SDK для Java версии 4](migrate-java-v4-sdk.md) для получения справки по обновлению до версии v4.
>

В этой статье рассматриваются распространенные проблемы, способы их устранения и средства диагностики при использовании Azure Cosmos DB пакета SDK для Java v4 с учетными записями Azure Cosmos DB API SQL.
Azure Cosmos DB пакет SDK для Java v4 предоставляет логическое представление на стороне клиента для доступа к Azure Cosmos DB API SQL. В этой статье описываются средства и подходы, которые помогут вам, если вы столкнетесь с проблемами.

Начните с этого списка:

* Ознакомьте с разделом [Распространенные проблемы и их обходные решения] в этой статье.
* Ознакомьтесь с пакетом SDK для Java в центре Azure Cosmos DB Central, который доступен [на сайте GitHub с открытым кодом](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos). Он имеет [раздел проблем](https://github.com/Azure/azure-sdk-for-java/issues), который активно отслеживается. Вы можете там найти уже готовое обходное решение похожей проблемы. Одним из полезных советов является фильтрация проблем с помощью тега *Cosmos: v4-Item* .
* Ознакомьтесь с [советами по повышению производительности](performance-tips-java-sdk-v4-sql.md) Azure Cosmos DB пакета SDK для Java v4 и следуйте предложенным рекомендациям.
* Следуйте указаниям в оставшейся части этой статьи, если решение не нашлось. Затем [сообщите о проблеме в GitHub](https://github.com/Azure/azure-sdk-for-java/issues). Если есть возможность добавить теги к вопросу GitHub, добавьте тег *Cosmos: v4-Item* .

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Распространенные проблемы и обходные решения для них

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Проблемы с сетью, ошибка из-за истечения времени ожидания чтения Netty, низкая пропускная способность, высокая задержка

#### <a name="general-suggestions"></a>Общие рекомендации
Обеспечить наилучшую производительность можно так.
* Убедитесь, что приложение выполняется в том же регионе, который указан для вашей учетной записи Azure Cosmos DB. 
* Проверьте использование ЦП на узле, где выполняется приложение. Если загрузка ЦП составляет 50% и более, запустите приложение на узле с более высокой конфигурацией. Можно также распределить нагрузку на большее число компьютеров.
    * Если вы используете приложение в службе Kubernetes Azure, вы можете [использовать Azure Monitor для мониторинга загрузки ЦП](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-analyze).

#### <a name="connection-throttling"></a>Регулирование подключения
Регулирование подключения может произойти из-за [ограничения числа подключений на узле] или [нехватки портов Azure SNAT (PAT)].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Ограничение числа подключений на узле
Некоторые системы Linux (например, Red Hat) имеют ограничение на общее число открытых файлов. Сокеты в Linux реализованы как файлы, поэтому это число также существенно ограничивает общее число подключений.
Выполните следующую команду.

```bash
ulimit -a
```
Максимальное разрешенное количество открытых файлов, которые определены как "nofile", должно быть по крайней мере вдвое больше, чем размер пула подключений. Дополнительные сведения см. в статье Azure Cosmos DB [Советы по повышению производительности](performance-tips-java-sdk-v4-sql.md)пакета SDK для Java версии 4.

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Нехватка портов SNAT (PAT) Azure

Если ваше приложение развернуто в службе "Виртуальные машины Azure" без общедоступного IP-адреса, по умолчанию [порты Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) используются для установления подключений к любой конечной точке вне вашей виртуальной машины. Количество разрешенных подключений от виртуальной машины к конечной точке Azure Cosmos DB ограничивается [конфигурацией Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Порты Azure SNAT используются, только если у виртуальной машины есть частный IP-адрес и процесс на виртуальной машине пытается установить подключение к общедоступному IP-адресу. Избежать ограничений Azure SNAT можно двумя способами:

* Добавьте конечную точку службы Azure Cosmos DB к подсети виртуальной сети для службы "Виртуальные машины Azure". Дополнительные сведения см. в статье [Конечные точки служб для виртуальной сети Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    При включении конечной точки службы запросы больше не отправляются с общедоступного IP-адреса в Azure Cosmos DB. Вместо этого отправляются идентификаторы виртуальной сети и подсети. Это изменение может привести к сбою брандмауэра, если разрешены только общедоступные IP-адреса. Если вы используете брандмауэр, при включении конечной точки службы добавьте подсеть в брандмауэре с помощью [списков управления доступом для виртуальных сетей](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Назначьте общедоступный IP-адрес виртуальной машине Azure.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Не удается связаться со службой — брандмауэр
``ConnectTimeoutException``Указывает, что пакет SDK не может связаться со службой.
При использовании прямого режима может возникнуть ошибка, аналогичная приведенной ниже:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Если на компьютере приложения работает брандмауэр, откройте диапазон портов 10 000 на 20 000, которые используются в режиме прямого подключения.
Кроме того, следует соблюдать [ограничение на количество подключений на хост-компьютере](#connection-limit-on-host).

#### <a name="http-proxy"></a>Прокси-сервер HTTP

При использовании прокси-сервера HTTP убедитесь, что он может поддерживать число подключений, указанное в свойстве `ConnectionPolicy` пакета SDK.
В противном случае возникнут проблемы с подключением.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Недопустимый шаблон кодировки: блокировка потока ввода-вывода Netty

Пакет SDK использует библиотеку [Netty](https://netty.io/) для ввода-вывода, чтобы связываться с Azure Cosmos DB. Пакет SDK содержит асинхронный API и использует неблокирующие API-интерфейсы ввода-вывода для NETTY. Операции ввода-вывода, присущие пакету SDK, выполняются в потоках Netty для ввода-вывода. Число потоков Netty для ввода-вывода настроено так, чтобы совпадать с числом ядер ЦП компьютера, где выполняется приложение. 

Потоки Netty для ввода-вывода предназначены только для операций Netty для ввода-вывода без блокировки. Пакет SDK возвращает результат вызова API, касающийся одного из потоков Netty для ввода-вывода, в код приложения. Если приложение выполняет операцию длительное время после получения результатов касательно потока Netty, пакет SDK может не располагать достаточным количеством потоков ввода-вывода для выполнения внутренних операций ввода-вывода. Кодирование такого приложения может привести к низкой пропускной способности, длительной задержке и сбоям `io.netty.handler.timeout.ReadTimeoutException`. Обходное решение заключается в том, чтобы переключить поток, когда вы знаете, что операция займет некоторое время.

Например, рассмотрим следующий фрагмент кода, который добавляет элементы в контейнер (см. [руководство по настройке](create-sql-api-java.md) базы данных и контейнера). Вы можете выполнять длительную работу, которая занимает более нескольких миллисекунд в потоке NETTY. В таком случае рано или поздно возникнет состояние, при котором не будет потока Netty для ввода-вывода, способного обрабатывать операции ввода-вывода. В результате произойдет сбой ReadTimeoutException.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>Асинхронный API пакета SDK для Java V4 (Maven com. Azure:: Azure-Cosmos)

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
  int requestTimeoutInSeconds = 10;
  ConnectionPolicy policy = new ConnectionPolicy();
  policy.setRequestTimeout(Duration.ofMillis(requestTimeoutInSeconds * 1000));
  AtomicInteger failureCount = new AtomicInteger();
  // Max number of concurrent item inserts is # CPU cores + 1
  Flux<Family> familyPub = 
      Flux.just(Families.getAndersenFamilyItem(), Families.getWitherspoonFamilyItem(), Families.getCarltonFamilyItem());
  familyPub.flatMap(family -> {
      return container.createItem(family);
  }).flatMap(r -> {
      try {
          // Time-consuming work is, for example,
          // writing to a file, computationally heavy work, or just sleep.
          // Basically, it's anything that takes more than a few milliseconds.
          // Doing such operations on the IO Netty thread
          // without a proper scheduler will cause problems.
          // The subscriber will get a ReadTimeoutException failure.
          TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
      } catch (Exception e) {
      }
      return Mono.empty();
  }).doOnError(Exception.class, exception -> {
      failureCount.incrementAndGet();
  }).blockLast();
  assert(failureCount.get() > 0);
}
```

Обходное решение заключается в изменении потока, в котором вы выполняете длительные операции. Определите отдельный экземпляр планировщика для приложения.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Асинхронный API пакета SDK для Java V4 (Maven com. Azure:: Azure-Cosmos)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = Schedulers.fromExecutor(ex);
```
Может потребоваться выполнить длительную работу (например, такую, которая требует значительных вычислений или блокировки ввода-вывода). В этом случае необходимо переключение потока на рабочую роль, предоставляемую параметром `customScheduler` с помощью API `.publishOn(customScheduler)`.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>Асинхронный API пакета SDK для Java V4 (Maven com. Azure:: Azure-Cosmos)

```java
container.createItem(family)
    .publishOn(customScheduler) // Switches the thread.
    .subscribe(
        // ...
    );
```
Используя `publishOn(customScheduler)`, вы освобождаете поток Netty для ввода-вывода и переключаетесь на собственный поток, предоставляемый настраиваемым планировщиком. Это изменение позволяет решить проблему. Сбой `io.netty.handler.timeout.ReadTimeoutException` больше возникать не будет.

### <a name="request-rate-too-large"></a>Высокая частота запросов
Этот сбой происходит на сервере. Он означает, что вы использовали подготовленную пропускную способность. Повторите попытку позже. Если эта ошибка возникает часто, попробуйте увеличить пропускную способность коллекции.

* **Применение интервала задержки getRetryAfterInMilliseconds.**

    Во время проверки производительности следует увеличивать нагрузку до тех пор, пока регулирование не будет выполняться при небольшой частоте запросов. При регулировании клиентское приложение должно реализовать интервал частоты повтора для частоты повтора сервера. Это гарантирует, что время ожидания между повторными попытками будет минимальным.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Ошибка подключения к эмулятору Azure Cosmos DB

Сертификат HTTPS эмулятора для Azure Cosmos DB является самозаверяющим. Чтобы SDK работал с эмулятором, необходимо импортировать сертификат эмулятора в Java TrustStore. Дополнительные сведения см. в статье [Экспорт сертификатов эмулятора для Azure Cosmos DB](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Проблемы с конфликтом зависимостей

Пакет SDK для Azure Cosmos DB Java извлекает несколько зависимостей; в общем случае, если дерево зависимостей проекта включает более раннюю версию артефакта, от которой зависит Azure Cosmos DB пакет SDK для Java, это может привести к непредвиденным ошибкам при запуске приложения. Если отладка объясняется тем, почему приложение неожиданно выдает исключение, рекомендуется дважды проверить, что дерево зависимостей случайно не извлекает более раннюю версию одного или нескольких Azure Cosmos DB зависимостей пакета SDK для Java.

Чтобы решить эту проблему, можно выяснить, какие из зависимостей проекта потребуются в старой версии, и исключить транзитивное зависимость от этой старой версии и разрешить Azure Cosmos DB пакета SDK для Java в более новую версию.

Чтобы узнать, какие зависимости проекта используют более старую версию чего-то, что Azure Cosmos DB пакет SDK для Java, выполните следующую команду в файле Project POM. XML:
```bash
mvn dependency:tree
```
Дополнительные сведения см. в разделе [руководств по дереву зависимостей Maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Когда вы узнаете, какая зависимость проекта зависит от более старой версии, вы можете изменить зависимость от этой библиотеки в файле POM и исключить транзитивное зависимость, следуя приведенному ниже примеру (предполагается, что *реактора-Core* является устаревшей зависимостью):

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Дополнительные сведения см. в разделе [исключение транзитивных зависимостей](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Включение ведения журнала для пакета SDK клиента

Azure Cosmos DB пакет SDK для Java V4 использует SLF4j в качестве фасадной ведения журналов, который поддерживает вход в популярные платформы ведения журналов, такие как log4j и logback.

Например, если вы хотите использовать log4j как платформу ведения журналов, добавьте в путь к классу Java следующие библиотеки:

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Добавьте также конфигурацию log4j.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Дополнительные сведения см. в [руководстве по ведению журналов с использованием sfl4j](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Сетевая статистика ОС
Запустите команду netstat, чтобы понять, сколько подключений находится в состоянии `ESTABLISHED`, `CLOSE_WAIT` и т. д.

В Linux вы можете запустить следующую команду:
```bash
netstat -nap
```

В Windows можно выполнить ту же команду с другими флагами аргументов:
```bash
netstat -abn
```

Отфильтруйте результат для отображения только подключений к конечной точке Azure Cosmos DB.

Количество подключений к конечной точке Azure Cosmos DB в состоянии `ESTABLISHED` не должно превышать настроенного размера пула подключений.

Много подключений к конечной точке Azure Cosmos DB могут быть в состоянии `CLOSE_WAIT`. Возможно, более 1000. Такое большое количество указывает, что подключения быстро устанавливаются и быстро разрываются. Подобная ситуация может привести к проблемам. Дополнительные сведения см. в разделе [Распространенные проблемы и их обходные решения].

 <!--Anchors-->
[Распространенные проблемы и обходные решения для них]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Ограничение числа подключений на хост-компьютере]: #connection-limit-on-host
[Нехватка портов Azure SNAT (PAT)]: #snat


