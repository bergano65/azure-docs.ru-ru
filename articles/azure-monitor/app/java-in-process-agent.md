---
title: Azure Monitor Application Insights Java
description: Наблюдение за производительностью приложений Java, выполняющихся в любой среде, без необходимости изменения кода. Распределенная трассировка и схема приложения.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 8423443abac90b87349a4a80fce0ec33a8b686da
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444747"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Application Insights Azure Monitor отслеживания приложений Java с некодированным кодом

Использовать мониторинг приложений Java без написания кода очень просто: не нужно изменять код, а агент Java можно включить, лишь немного изменив конфигурацию.

 Агент Java работает в любой среде и позволяет отслеживать все приложения Java. Другими словами, независимо от того, выполняются ли приложения Java на виртуальных машинах (локально), в AKS в Windows, Linux — Вы переименовываете его, а агент Java 3,0 будет отслеживать ваше приложение.

Добавление пакета SDK для Application Insights Java в приложение больше не требуется, так как агент 3,0 выполняет автоматический сбор запросов, зависимостей и журналов отдельно.

Вы по-прежнему можете отправить пользовательскую телеметрию из приложения. Агент 3,0 будет отслеживать и сопоставлять его вместе со всеми автособираемыми данными телеметрии.

Агент 3,0 поддерживает Java 8 и более поздних версий.

## <a name="quickstart"></a>Краткое руководство

**1. Скачайте агент**

> [!WARNING]
> **При обновлении с предварительной версии 3,0**
>
> Тщательно изучите все [Параметры конфигурации](./java-standalone-config.md) , так как структура JSON была полностью изменена, а не только само имя файла, что и все строчные буквы.

Скачать [аппликатионинсигхтс-ажент-3.0.0. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0/applicationinsights-agent-3.0.0.jar)

**2. Указание ВИРТУАЛЬНОЙ машины Java агенту**

Добавить `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` в виртуальной машины Java argss вашего приложения

Стандартные аргументы ВИРТУАЛЬНОЙ машины Java включают `-Xmx512m` и `-XX:+UseG1GC` . Итак, если вы узнаете, куда добавить эти данные, вы уже знакомы с их добавлением.

Дополнительные сведения о настройке аргументов ВИРТУАЛЬНОЙ машины Java приложения см. в статье [Советы по обновлению аргументов виртуальной машины Java](./java-standalone-arguments.md).

**3. Наведите агент на ресурс Application Insights**

Если у вас еще нет ресурса Application Insights, можно создать новый, выполнив действия, описанные в [руководстве по созданию ресурсов](./create-new-resource.md).

Наведите агент на ресурс Application Insights, задав переменную среды:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Или создайте файл конфигурации с именем `applicationinsights.json` и поместите его в тот же каталог, что и `applicationinsights-agent-3.0.0.jar` , со следующим содержимым:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Строку подключения можно найти в ресурсе Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Строка подключения Application Insights":::

**4. Вот и все!**

Теперь запустите приложение и перейдите к ресурсу Application Insights в портал Azure, чтобы просмотреть данные мониторинга.

> [!NOTE]
> Для отображения данных мониторинга на портале может потребоваться несколько минут.


## <a name="configuration-options"></a>Варианты настройки

В `applicationinsights.json` файле можно дополнительно настроить:

* Имя облачной роли
* Экземпляр облачной роли
* Выборка
* Метрики JMX
* Пользовательские измерения
* Обработчики данных телеметрии (Предварительная версия)
* Автоматическое собираемое ведение журнала
* Автособираемые метрики Микрометер (включая метрики загрузчика пружины)
* Пульс
* Прокси-сервер HTTP
* Самостоятельная диагностика

Полные сведения см. в разделе [Параметры конфигурации](./java-standalone-config.md) .

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Автоматические собранные запросы, зависимости, журналы и метрики

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
* Log4j (включая свойства MDC)
* SLF4J/Logback (включая свойства MDC)

### <a name="metrics"></a>Метрики

* Микрометер (включая метрики огнетушителя пружинной загрузки)
* Метрики JMX

## <a name="sending-custom-telemetry-from-your-application"></a>Отправка пользовательских данных телеметрии из приложения

Наша цель в версии 3.0 + позволяет отправить пользовательскую телеметрию с помощью стандартных API.

Мы поддерживаем Микрометер, Опентелеметри API и популярные платформы ведения журналов. Application Insights Java 3,0 автоматически захватывает данные телеметрии и сопоставляет их вместе со всеми автоматически собираемыми данными телеметрии.

### <a name="supported-custom-telemetry"></a>Поддерживаемые пользовательские данные телеметрии

Приведенная ниже таблица представляет сейчас поддерживаемые пользовательские типы телеметрии, которые можно включить в дополнение к агенту Java 3,0. Чтобы суммировать, пользовательские метрики поддерживаются через микрометер, пользовательские исключения и трассировки можно включить с помощью платформ ведения журналов, а любой тип пользовательской телеметрии поддерживается с помощью [пакета SDK для Java 2. x Application Insights](#sending-custom-telemetry-using-application-insights-java-sdk-2x). 

|                     | Micrometer | Log4j, logback, Июл | пакет SDK для 2. x |
|---------------------|------------|---------------------|---------|
| **Пользовательские события**   |            |                     |  Да    |
| **Пользовательские метрики**  |  Да       |                     |  Да    |
| **Зависимости**    |            |                     |  Да    |
| **Исключения**      |            |  Да                |  Да    |
| **Просмотры страницы**      |            |                     |  Да    |
| **Запросы**        |            |                     |  Да    |
| **Трассировки**          |            |  Да                |  Да    |

В настоящее время мы не планируем выпустить пакет SDK с Application Insights 3,0.

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

Если вы уже используете Application Insights Java SDK 2. x в своем приложении, нет необходимости удалять его.
Агент Java 3,0 определит его, а затем запишет и сопоставьте любые пользовательские данные телеметрии, отправляемые через пакет SDK для Java 2. x, и подавление автоматической сбора данных, выполненных пакетом SDK для Java 2. x, для предотвращения дублирования данных телеметрии.

Если используется агент Application Insights 2. x, необходимо удалить `-javaagent:` аргумент виртуальной машины Java, указывающий на агент 2. x.

> [!NOTE]
> Пакет SDK для Java 2. x Telemetryinitializer и TelemetryProcessors не будет выполняться при использовании агента 3,0.
> Многие из вариантов использования, которые ранее были необходимы, можно разрешить в 3,0, настроив [пользовательские измерения](./java-standalone-config.md#custom-dimensions) или настроив [обработчики данных телеметрии](./java-standalone-telemetry-processors.md).

> [!NOTE]
> 3,0 еще не поддерживает несколько ключей инструментирования в одном ВИРТУАЛЬНОЙ машины Java.
