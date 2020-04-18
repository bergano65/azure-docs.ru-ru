---
title: Мониторинг Java-приложений в любой среде - Azure Monitor Application Insight
description: Мониторинг производительности приложений для Java-приложений, работающих в любой среде без инструментирования приложения. Распределенная карта отслеживания и применения.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 5706d5a74bd6850a237f7418b1a86a8e9c7762e1
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604574"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java без кода приложение мониторинга Azure Monitor Application Insights - публичный предварительный просмотр

Мониторинг приложений без кода Java — это простота — нет никаких изменений кода, Java-агент может быть включен через несколько изменений конфигурации.

 Агент Java работает в любой среде и позволяет отслеживать все java-приложения. Другими словами, независимо от того, работаете ли вы с Java-приложениями на ввозах, в помещениях, в AKS, на Windows, Linux - вы называете это, агент Java 3.0 будет следить за вашим приложением.

Добавление приложения Insights Java SDK в приложение больше не требуется, так как агент 3.0 автоматически собирает запросы, зависимости и регистрирует все самостоятельно.

Вы все еще можете отправить пользовательские телеметрии из вашего приложения. Агент 3.0 будет отслеживать и соотносить его вместе со всей автособранной телеметрией.

## <a name="quickstart"></a>Краткое руководство

**1. Скачать агента**

Скачать [applicationinsights-агент-3.0.0-PREVIEW.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.3/applicationinsights-agent-3.0.0-PREVIEW.3.jar)

**2. Направьте JVM агенту**

Добавьте `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.3.jar` к JVM args вашего приложения

Типичные JVM `-Xmx512m` args включают и `-XX:+UseG1GC`. Так что если вы знаете, где добавить их, то вы уже знаете, где добавить это.

Для дополнительной помощи при настройке JVM args вашего приложения, пожалуйста, [см. 3.0 Предварительный просмотр: Советы по обновлению JVM args](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Tips-for-updating-your-JVM-args).

**3. Направьте агента на ресурс Application Insights**

Если у вас еще нет ресурса Application Insights, можно создать новый, выявив шаги в руководстве по [созданию ресурсов.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

Направьте агента на ресурс Application Insights, установив переменную среды:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Или, создав файл `ApplicationInsights.json`конфигурации с и поместить `applicationinsights-agent-3.0.0-PREVIEW.3.jar`его в тот же каталог, что и , со следующим содержанием:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Строку подключения можно найти в ресурсе Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Строка подключения к приложениям Insights":::

**4. Вот и все!**

Теперь запустите приложение и перейдите на ресурс Application Insights на портале Azure, чтобы просмотреть данные мониторинга.

> [!NOTE]
> Отображение данных мониторинга на портале может занять несколько минут.


## <a name="configuration-options"></a>Варианты настройки

В `ApplicationInsights.json` файле можно дополнительно настроить:

* Имя роли облака
* Экземпляр облачной роли
* Захват журнала приложения
* Метрики JMX
* Micrometer
* Пульс
* Выборка
* HTTP Прокси
* Самодиагностика

Подробности смотрите по [адресу 3.0 Public Preview: Параметры конфигурации](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Configuration-Options).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Автособранные запросы, зависимости, журналы и метрики

### <a name="requests"></a>Requests

* Потребители JMS
* Кафка Потребители
* Нетти/ВебФлюкс
* Сервлеты
* Весеннее планирование

### <a name="dependencies-with-distributed-trace-propagation"></a>Зависимости с распределенным распространением следов

* Apache httpclient и HttpAsyncclient
* gRPC
* java.net.httpURLConnection
* JMS
* Kafka
* Нетти-клиент
* OkHttp

### <a name="other-dependencies"></a>Другие зависимости

* Cassandra
* JDBC
* MongoDB (асинисии и синхронизации)
* Редис (Латук и Джедаи)

### <a name="logs"></a>Журналы

* java.util.logging
* Log4j
* SLF4J/Logback

### <a name="metrics"></a>Метрики

* Micrometer
* JMX Метрики

## <a name="sending-custom-telemetry-from-your-application"></a>Отправка пользовательской телеметрии из приложения

Наша цель в 3.0 ", чтобы позволить вам отправить пользовательские телеметрии с помощью стандартных AIS.

Мы поддерживаем Micrometer, OpenTelemetry API и популярные платформы лесозаготовок. Приложение Insights Java 3.0 автоматически захватывает телеметрию и соотносит ее вместе со всей автособранной телеметрией.

По этой причине мы не планируем выпускать SDK с Application Insights 3.0 в это время.

Application Insights Java 3.0 уже прослушивает телеметрию, которая отправляется в Application Insights Java SDK 2.x. Эта функциональность является важной частью истории обновления для существующих пользователей 2.x, и она заполняет важный пробел в нашей пользовательской поддержке телеметрии до тех пор, пока OpenTelemetry API не будет GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Отправка пользовательской телеметрии с помощью Application Insights Java SDK 2.x

Добавьте `applicationinsights-core-2.6.0.jar` в приложение (все версии 2.x поддерживаются Application Insights Java 3.0, но стоит использовать последние, если у вас есть выбор):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Создайте телеметрическийклиент:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

и использовать это для отправки пользовательских телеметрии.

### <a name="events"></a>События

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Метрики

Вы можете отправить метрическую телеметрию через [Micrometer:](https://micrometer.io)

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Или вы также можете использовать Application Insights Java SDK 2.x:

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>Зависимости

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>Журналы
Вы можете отправлять пользовательские телеметрии журнала через ваш любимый журнал инфраструктуры.

Или вы также можете использовать Application Insights Java SDK 2.x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Исключения
Вы можете отправить пользовательские телеметрии исключения через ваш любимый журнал платформы.

Или вы также можете использовать Application Insights Java SDK 2.x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Обновление с application Insights Java SDK 2.x

Если вы уже используете Application Insights Java SDK 2.x в приложении, нет необходимости его удалять. Агент Java 3.0 будет обнаруживать его, а также фиксировать и соотносить любую пользовательскую телеметрию, которую вы отправляете через Java SDK 2.x, подавляя при этом любую автоколлекцию, выполняемую Java SDK 2.x для предотвращения дублирования.

> [!NOTE]
> Примечание: Java SDK 2.x Telemetry Initializers и TelemetryProcessors не будут работать при использовании агента 3.0.
