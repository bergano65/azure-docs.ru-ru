---
title: Мониторинг приложений Java в любой среде — Azure Monitor Application Insights
description: Мониторинг производительности приложений Java, выполняющихся в любой среде, без инструментирования приложения. Распределенная трассировка и схема приложения.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 591cfad0f4719595835f212b9205354aad7cb9e8
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508077"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Azure Monitor Мониторинг приложений Java с поддержкой кода Application Insights — общедоступная Предварительная версия

Наблюдение за приложениями без кода Java — это простота — нет изменений в коде, агент Java можно включить с помощью всего лишь нескольких изменений конфигурации.

 Агент Java работает в любой среде и позволяет отслеживать все приложения Java. Другими словами, независимо от того, выполняются ли приложения Java на виртуальных машинах (локально), в AKS в Windows, Linux — Вы переименовываете его, а агент Java 3,0 будет отслеживать ваше приложение.

Добавление пакета SDK для Application Insights Java в приложение больше не требуется, так как агент 3,0 выполняет Автозапись запросов, зависимостей и журналов отдельно.

Вы по-прежнему можете отправить пользовательскую телеметрию из приложения. Агент 3,0 будет отслеживать и сопоставлять его вместе со всеми данными телеметрии, собранными в ходе сбора.

## <a name="quickstart"></a>Краткое руководство

**1. Скачайте агент**

Скачайте [аппликатионинсигхтс-ажент-3.0.0-превиев. 4. jar.](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.4/applicationinsights-agent-3.0.0-PREVIEW.4.jar)

**2. Указание ВИРТУАЛЬНОЙ машины Java агенту**

Добавить `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.4.jar` в виртуальной машины Java argss вашего приложения

Стандартные аргументы ВИРТУАЛЬНОЙ машины Java включают `-Xmx512m` и `-XX:+UseG1GC`. Итак, если вы узнаете, куда добавить эти данные, вы уже знакомы с их добавлением.

Дополнительные сведения о настройке аргументов ВИРТУАЛЬНОЙ машины Java приложения см. в разделе [3,0 Preview: советы по обновлению аргументов виртуальной машины Java](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-arguments).

**3. Наведите агент на ресурс Application Insights**

Если у вас еще нет ресурса Application Insights, можно создать новый, выполнив действия, описанные в [руководстве по созданию ресурсов](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

Наведите агент на ресурс Application Insights, задав переменную среды:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Или создайте файл конфигурации с именем `ApplicationInsights.json`и поместите его в тот же каталог, что `applicationinsights-agent-3.0.0-PREVIEW.4.jar`и, со следующим содержимым:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Строку подключения можно найти в ресурсе Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Строка подключения Application Insights":::

**4. Вот и все!**

Теперь запустите приложение и перейдите к ресурсу Application Insights в портал Azure, чтобы просмотреть данные мониторинга.

> [!NOTE]
> Для отображения данных мониторинга на портале может потребоваться несколько минут.


## <a name="configuration-options"></a>Параметры конфигурации

В `ApplicationInsights.json` файле можно дополнительно настроить:

* Имя облачной роли
* Экземпляр облачной роли
* Запись журнала приложений
* Метрики JMX
* Micrometer
* Пульс
* Выборка
* Прокси-сервер HTTP
* Самостоятельная диагностика

Дополнительные сведения см. в статье [3,0 общедоступная Предварительная версия: параметры конфигурации](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Автособранные запросы, зависимости, журналы и метрики

### <a name="requests"></a>Requests

* Потребители JMS
* Потребители Kafka
* NETTY/Вебфлукс
* Сервлеты
* Планирование весны

### <a name="dependencies-with-distributed-trace-propagation"></a>Зависимости с распространением распределенной трассировки

* Apache HttpClient и Хттпасинкклиент
* gRPC
* Java. NET. Хттпурлконнектион
* JMS
* Kafka
* Клиент NETTY
* OkHttp

### <a name="other-dependencies"></a>Другие зависимости

* Cassandra
* JDBC
* MongoDB (Async и Sync)
* Redis (салат и Jedis)

### <a name="logs"></a>Журналы

* Java. util. Logging
* Log4j
* SLF4J/Logback

### <a name="metrics"></a>Метрики

* Микрометер (включая метрики огнетушителя пружинной загрузки)
* Метрики JMX

## <a name="sending-custom-telemetry-from-your-application"></a>Отправка пользовательских данных телеметрии из приложения

Наша цель в версии 3.0 + позволяет отправить пользовательскую телеметрию с помощью стандартных API.

Мы поддерживаем Микрометер, Опентелеметри API и популярные платформы ведения журналов. Application Insights Java 3,0 автоматически захватывает данные телеметрии и сопоставляет их вместе со всеми автоматически собранными данными телеметрии.

По этой причине мы не планируем выпустить пакет SDK с Application Insights 3,0 в настоящее время.

Application Insights Java 3,0 уже прослушивает данные телеметрии, отправляемые в пакет SDK для Java Application Insights 2. x. Эта функция является важной частью истории обновления для существующих пользователей версии 2. x, и она заполняет важный зазор в нашей пользовательской поддержке телеметрии, пока интерфейс API Опентелеметри не будет общедоступен.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Отправка пользовательской телеметрии с помощью Application Insights пакета SDK для Java 2. x

Добавьте `applicationinsights-core-2.6.0.jar` в приложение (все версии 2. x поддерживаются Application Insights Java 3,0, но следует использовать последнюю версию, если у вас есть вариант):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Создайте TelemetryClient:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

и используют его для отправки пользовательской телеметрии.

### <a name="events"></a>События

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Метрики

Данные телеметрии метрик можно отправить через [микрометер](https://micrometer.io):

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Также можно использовать Application Insights пакет SDK для Java 2. x:

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
Вы можете отправить пользовательские данные телеметрии журналов с помощью избранной платформы ведения журналов.

Также можно использовать Application Insights пакет SDK для Java 2. x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Исключения
Вы можете отправить пользовательскую телеметрию исключений с помощью избранной платформы ведения журналов.

Также можно использовать Application Insights пакет SDK для Java 2. x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Обновление с Application Insights пакета SDK для Java 2. x

Если вы уже используете Application Insights Java SDK 2. x в своем приложении, нет необходимости удалять его. Агент Java 3,0 определит его, а затем запишет и сопоставьте любые пользовательские данные телеметрии, отправляемые через пакет SDK для Java 2. x, и подавление автосбора данных, выполненных пакетом SDK для Java 2. x, для предотвращения дублирования.

> [!NOTE]
> Примечание. пакет SDK для Java 2. x Telemetryinitializer и TelemetryProcessors не будет выполняться при использовании агента 3,0.
