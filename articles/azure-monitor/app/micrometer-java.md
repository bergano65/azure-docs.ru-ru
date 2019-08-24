---
title: Как использовать Micrometer с пакетом SDK Azure Application Insights для Java | Документация Майкрософт
description: 'Поэтапное руководство по использованию Micrometer в приложениях Spring Boot и других приложениях для Application Insights. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: lagayhar
ms.openlocfilehash: 1074495f5ac9112b6ce4f67ad2d81ee57b28e720
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012694"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Как использовать Micrometer с пакетом SDK Azure Application Insights для Java
Мониторинг приложений Micrometer измеряет метрики для кода приложения на основе виртуальной машины Java и позволяет экспортировать данные в предпочитаемые системы мониторинга. В этой статье вы узнаете, как использовать Micrometer с Application Insights для приложений Spring Boot и других приложений.

## <a name="using-spring-boot-15x"></a>Использование Spring Boot 1.5x
Добавьте следующие зависимости в файл pom.xml или build.gradle: 
* [spring-boot-starter для Application Insights](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter) версии 1.1.0-BETA или более поздней.
* Реестр Azure Micrometer 1.1.0 или более поздней версии.
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 или более поздней версии (возвращение кода автонастройки на платформе Spring к более ранней версии).
* [Ресурс ApplicationInsights](../../azure-monitor/app/create-new-resource.md )

Шаги

1. Обновите файл pom.xml приложения Spring Boot и включите в него следующие зависимости:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>1.1.0-BETA</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Обновите файл application.properties или yml с помощью ключа инструментирования Application Insights, используя следующее свойство:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Создайте и запустите приложение.
2. Выполнив указанные выше инструкции, вы сможете приступить к работе с предварительно агрегированными метриками, автоматически собранными в Azure Monitor. Подробнее о том, как настроить начальное приложение Spring Boot Application Insights, см. на странице с [файлом сведений на GitHub](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Использование Spring 2.x

Добавьте следующие зависимости в файл pom.xml или build.gradle:

* spring-boot-starter для Application Insights версии 2.1.2 или более поздней.
* Azure-пружины-Boot — метрики — запуски 2.1.5 или выше  
* [Ресурс Application Insights](../../azure-monitor/app/create-new-resource.md ).

Шаги:

1. Обновите файл pom.xml приложения Spring Boot и включите в него следующую зависимость:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.1.6</version>
    </dependency>
    ```
1. Обновите файл application.properties или yml с помощью ключа инструментирования Application Insights, используя следующее свойство:

     `management.metrics.export.azuremonitor.instrumentation-key=<your-instrumentation-key-here>`
3. Создайте и запустите приложение.
4. Выполнив указанные выше инструкции, вы сможете приступить к работе с предварительно агрегированными метриками, автоматически собранными в Azure Monitor. Подробнее о том, как настроить начальное приложение Spring Boot Application Insights, см. на странице с [файлом сведений на GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Метрики по умолчанию:

*    Автоматически настроенные метрики для Tomcat, виртуальной машины Java, метрики Logback, Log4J, метрики работоспособности системы, метрики процессора, FileDescriptorMetrics.
*    Например, если netflix hystrix присутствует в пути класса, мы также получаем эти метрики. 
*    Доступ к следующим метрикам можно получить, добавив соответствующие компоненты. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcaseCache, Jcache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - Метрики OkHttp3 
        - Метрики Kafka 

 

Отключение автоматического сбора метрик: 
 
- Метрики виртуальной машины Java: 
    - management.metrics.binders.jvm.enabled=false 
- Метрики Logback: 
    - management.metrics.binders.logback.enabled=false
- Метрики времени работы: 
    - management.metrics.binders.uptime.enabled=false 
- Метрики процессора:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.Metrics.binders.Files.Enabled=false 
- Метрики Hystrix если библиотека на classpath: 
    - management.Metrics.binders.hystrix.Enabled=false 
- Метрики AspectJ (если библиотека указана в пути к классу): 
    - spring.AOP.Enabled=false 

> [!NOTE]
> Укажите свойства выше в файле application.properties или application.yml своего приложения Spring Boot.

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Использование Micrometer со сторонними веб-приложениями

Добавьте следующие зависимости в файл pom.xml или build.gradle:
 
* [Application Insight Core 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.2.0) или более поздней версии
* [Application Insights Web 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/2.2.0) или более поздней версии.
* [Регистрация веб-фильтра](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started).
* Реестр Azure Micrometer 1.1.0 или более поздней версии.
* [Ресурс Application Insights](../../azure-monitor/app/create-new-resource.md ).

Шаги:

1. Добавьте следующие зависимости в файл pom.xml или build.gradle:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web</artifactId>
            <version>2.2.0</version>
        </dependency
     ```

2. Поместите Application Insights.xml в папку ресурсов.

    Пример класса Servlet (выводит метрику таймера):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

      Пример класса конфигурации:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

Дополнительные сведения о метриках см. в статье [Micrometer documentation](https://micrometer.io/docs/) (Документация по Micrometer).

Другой пример кода по созданию различных метрик можно найти в [официальном репозитории Micrometer на GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Связывание дополнительной коллекции метрик

### <a name="springbootspring"></a>SpringBoot/Spring

Создайте компонент соответствующей категории метрик. Например, предположим, нам нужен кэш метрик Guava:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Несколько метрик не включены по умолчанию, но их можно связать указанным выше способом. Полный список см. в [официальном репозитории Micrometer на GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Стороннее приложение
Добавьте следующий код привязки в файл конфигурации:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о Micrometer см. в официальной [документации по Micrometer](https://micrometer.io/docs).
* Сведения о Spring в Azure см. в [официальной документации](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).
