---
title: Диагностика и устранение неполадок с пакетом SDK Java Async для Azure Cosmos DB | Документация Майкрософт
description: Воспользуйтесь такими функциями, как ведение журнала на стороне клиента, и другими сторонними инструментами для выявления, диагностики и устранения проблем, связанных с Azure Cosmos DB.
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: ee92a5dd474cdf4f32ed2c7327d732a2cfbbbf79
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632940"
---
# <a name="troubleshooting-issues-when-using-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Устранение неполадок при использовании пакета SDK Async Java с учетными записями API SQL для Azure Cosmos DB
В этой статье рассматриваются распространенные проблемы при использовании [пакета SDK Java Async](sql-api-sdk-async-java.md) с учетными записями API SQL для Azure Cosmos DB, обходные пути, а также действия для диагностики и средства.
Пакет SDK Java Async предоставляет клиентское логическое представление для доступа к API SQL для Azure Cosmos DB. В этой статье описываются средства и подходы, которые помогут вам, если вы столкнетесь с проблемами.

Начните с этого списка:
    * Ознакомьте с разделом [Распространенные проблемы и их обходные решения] в этой статье.
    * [Наш пакет SDK с открытым исходным кодом размещен на GitHub](https://github.com/Azure/azure-cosmosdb-java), где есть [раздел для сообщения о проблемах](https://github.com/Azure/azure-cosmosdb-java/issues), который мы активно отслеживаем. Вы можете там найти обходное решение похожей проблемы.
    * Просмотрите [советы по повышению производительности](performance-tips-async-java.md) и следуйте рекомендациям.
    * Следуйте указаниям в оставшейся части этой статьи. Если вы не найдете решение, опубликуйте проблему [на сайте GitHub](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Распространенные проблемы и их обходные решения

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Проблемы с сетью, ошибка из-за истечения времени ожидания чтения Netty, низкая пропускная способность, высокая задержка

#### <a name="general-suggestions"></a>Общие рекомендации
* Убедитесь, что приложение выполняется в одном регионе с вашей учетной записью Cosmos DB. 
* Проверьте использование ЦП на узле, где выполняется приложение. Если загрузка процессора составляет 90 % или более, подумайте о том, чтобы запустить приложение на узле с более производительной конфигурацией, или распределите нагрузку среди большего количества компьютеров.

#### <a name="connection-throttling"></a>Регулирование подключения
Регулирование подключения может произойти из-за [Ограничение числа подключений на узле] или [Нехватка портов SNAT (PAT) Azure]:

##### <a name="connection-limit-on-host"></a>Ограничение числа подключений на узле
Некоторые дистрибутивы Linux (например, Red Hat) имеют ограничение на общее число открытых файлов. Сокеты в Linux реализованы как файлы, поэтому это число также существенно ограничивает общее число подключений.
Выполните следующую команду:

```bash
ulimit -a
```
Количество открытых файлов (nofile) должно быть достаточно большим (по крайней мере вдвое больше размера пула подключений). Подробные сведения см. в [советах по повышению производительности](performance-tips-async-java.md).

##### <a name="snat"></a>Нехватка портов SNAT (PAT) Azure

Если ваше приложение развернуто на виртуальной машине Azure без общедоступного IP-адреса, по умолчанию [порты Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) используются для установления подключений к любой конечной точке вне вашей виртуальной машины. Количество разрешенных подключений от виртуальной машины к конечной точке Cosmos DB ограничивается [конфигурацией Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

Порты Azure SNAT используются только в том случае, если у виртуальной машины Azure есть частный IP-адрес и процесс на виртуальной машине пытается установить подключение к общедоступному IP-адресу. Таким образом, избежать ограничений Azure SNAT можно двумя способами:
    * Добавьте конечную точку службы Azure Cosmos DB в подсеть виртуальной сети виртуальных машин Azure, как описано в статье [Конечные точки службы виртуальной сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). При включении конечной точки службы запросы больше не отправляются с общедоступного IP-адреса в Cosmos DB. Вместо этого отправляются удостоверения виртуальной сети и подсети. Это изменение может привести к сбою брандмауэра, если разрешены только общедоступные IP-адреса. Если вы используете брандмауэр, то при включении конечной точки службы к брандмауэру нужно добавить подсеть, используя [списки управления доступом виртуальной сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
    * Назначьте общедоступный IP-адрес виртуальной машине Azure.

#### <a name="http-proxy"></a>Прокси-сервер HTTP

При использовании прокси-сервера HTTP убедитесь, что он может поддерживать число подключений, настроенных в свойстве `ConnectionPolicy` пакета SDK.
В противном случае возникнут проблемы с подключением.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Недопустимый шаблон кодировки: блокировка потока ввода-вывода Netty

Пакет SDK использует библиотеку ввода-вывода [Netty](https://netty.io/) для связи со службой Azure Cosmos DB. У нас есть асинхронный API, и мы используем интерфейсы API ввода-вывода Netty без блокировки. Операции ввода-вывода пакета SDK выполняются в потоках ввода-вывода Netty. Число операций потоков ввода-вывода Netty настроено так, чтобы совпадать с числом ядер ЦП компьютера, где выполняется приложение. Потоки ввода-вывода Netty предназначены только для операций ввода-вывода Netty без блокировки. Пакет SDK возвращает результат вызова API в код приложения в одном из потоков ввода-вывода Netty. Если приложение после получения результатов в потоке Netty выполняет длительную операцию в этом потоке, это может привести к тому, что пакет SDK не будет иметь достаточного количества потоков ввода-вывода для выполнения своей внутренней работы. Кодирование такого приложения может привести к низкой пропускной способности, высокой задержке и сбоям `io.netty.handler.timeout.ReadTimeoutException`. Обходное решение заключается в том, чтобы переключить поток, когда вы знаете, что операция займет некоторое время.

   Например, в следующем фрагменте кода показано, что если вы выполняете в потоке netty долговременную работу, которая занимает больше нескольких миллисекунд, вы, в конечном итоге, можете попасть в состояние, в котором не останется потоков для обработки операций ввода-вывода, и в результате вы получите исключение ReadTimeoutException:
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   Обходное решение заключается в изменении потока, в котором вы выполняете длительные операции. Определите одноэлементный экземпляр планировщика для приложения:
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Всякий раз, когда вам нужно делать долгосрочную работу (например, интенсивную работу с вычислением, блокирующую операции ввода-вывода), с помощью API `.observeOn(customScheduler)` переключите поток на рабочий процесс, предоставленный вашим планировщиком `customScheduler`.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
Используя `observeOn(customScheduler)`, вы освобождаете поток ввода-вывода Netty и переключаетесь на собственный поток, предоставляемый планировщиком customScheduler. Эта модификация решит проблему, и вы больше не получите ошибку `io.netty.handler.timeout.ReadTimeoutException`.

### <a name="connection-pool-exhausted-issue"></a>Проблема исчерпания пула подключений

`PoolExhaustedException` является ошибкой на стороне клиента. Если эта ошибка возникает часто, это означает, что рабочая нагрузка приложения больше той, которую может обрабатывать пул подключений пакета SDK. Здесь может помочь увеличение размера пула подключений или распределение нагрузки между несколькими приложениями.

### <a name="request-rate-too-large"></a>Высокая частота запросов
Это ошибка на стороне сервера, указывающая, что вы потребили подготовленную пропускную способность и должны повторить попытку позже. Если эта ошибка возникает часто, попробуйте увеличить пропускную способность коллекции.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Ошибка подключения к эмулятору Azure Cosmos DB

Сертификат HTTPS эмулятора Cosmos DB является самозаверяющим. Чтобы SDK работал с эмулятором, необходимо импортировать сертификат эмулятора в Java TrustStore, как объясняется [здесь](local-emulator-export-ssl-certificates.md).


## <a name="enable-client-sice-logging"></a>Включение ведения журнала для пакета SDK клиента

Пакет SDK Async Java использует SLF4j как интерфейс ведения журнала, который поддерживает запись на популярные платформы ведения журналов, такие как log4j и logback.

Например, если вы хотите использовать log4j как платформу ведения журналов, добавьте в ваш путь к классу Java следующие библиотеки:

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

Добавьте также конфигурацию log4j:
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

Дополнительные сведения см. в [руководстве пользователя sfl4j](https://www.slf4j.org/manual.html).

## <a name="netstats"></a>Сетевая статистика ОС
Запустите команду netstat, чтобы понять, сколько подключений находится в состоянии `Established`, `CLOSE_WAIT` и т. д.

В Linux вы можете запустить следующую команду:
```bash
netstat -nap
```
Отфильтруйте результат для отображения только подключений к конечной точке Cosmos DB.

Количество подключений к конечной точке Cosmos DB в состоянии `Established` должно быть не больше настроенного размера пула подключений.

Если в состоянии `CLOSE_WAIT` пребывает много подключений к конечной точке Cosmos DB, например более 1000, это указывает на то, что они установлены и быстро разорваны, и это может вызвать проблемы. Дополнительные сведения см. в разделе [Распространенные проблемы и их обходные решения].

 <!--Anchors-->
[Распространенные проблемы и их обходные решения]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Ограничение числа подключений на узле]: #connection-limit-on-host
[Нехватка портов SNAT (PAT) Azure]: #snat


