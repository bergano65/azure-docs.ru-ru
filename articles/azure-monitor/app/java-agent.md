---
title: Мониторинг производительности веб-приложений Java в Azure Application Insights | Документация Майкрософт
description: Расширенный мониторинг производительности и использования веб-сайта Java с помощью Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: ce5f7ab1e6751a9ce68aa2d9c466a112c9cac182
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900614"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Мониторинг зависимостей, перехваченных исключений и времени выполнения методов в веб-приложениях Java


[Инструментирование веб-приложения Java с помощью Application Insights][java] позволяет получать более подробную информацию без изменения кода, используя для этого агент для Java.

* **Зависимости** — данные о вызовах других компонентов в вашем приложении, включая:
  * **Вызовы REST** через HttpClient, OkHttp и RestTemplate (Spring).
  * Вызовы **Redis** через клиент Jedis.
  * **[Вызовы JDBC](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** — автоматически включаются команды MySQL, SQL Server и Oracle DB. Если для MySQL вызов выполняется дольше 10 с, агент сообщает о плане запроса.
* **Перехваченные исключения.** Сведения об исключениях, обработанных вашим кодом.
* **Время выполнения метода.** Сведения о времени, которое потребовалось для выполнения определенных методов.

Чтобы использовать агент для Java, его необходимо установить на сервере. Веб-приложения необходимо инструментировать [пакетом SDK для Java Application Insights][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Установка агента Application Insights для Java
1. [Скачайте агент](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) на компьютер с сервером Java. Обязательно скачайте агент Java той же версии, что и веб-пакеты и пакет SDK для Java Application Insights.
2. Измените скрипт запуска сервера приложений, добавив следующую виртуальную машину Java:
   
    `javaagent:`*полный путь к файлу агента JAR*
   
    Например, в Tomcat на компьютере Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Перезапустите сервер приложений.

## <a name="configure-the-agent"></a>Настройка агента
Создайте файл с именем `AI-Agent.xml` и поместите его в ту же папку, где находится JAR-файл агента.

Настройка содержимого XML-файла. Измените приведенный ниже пример, включив необходимые функции или убрав ненужные.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />
           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Необходимо включить прием отчетов и контроль времени выполнения отдельных методов.

По умолчанию `reportExecutionTime` имеет значение true, а `reportCaughtExceptions` — значение false.

## <a name="additional-config-spring-boot"></a>Дополнительные конфигурации (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Для службы приложений выполните следующие действия.

* Выберите элементы "Параметры > Параметры приложения".
* В разделе "Параметры приложения" добавьте новую пару "ключ — значение":

Раздел: `JAVA_OPTS` Значение: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.3.1-SNAPSHOT.jar`

Последнюю версию агента Java см. выпуски [здесь](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

Агент должны быть упакованы как ресурс в своем проекте, таким образом, чтобы он оказывается в D:/home/site/wwwroot/directory. Убедитесь, что агент находится в правильный каталог службы приложений, выбрав **средства разработки** > **Дополнительные инструменты** > **консоли отладки**и изучение содержимого каталога узлов.    

* Сохранить изменения и перезапустите приложение. (Эти шаги применяются только в службах приложений под управлением Windows.)

> [!NOTE]
> Файлы агента AI-Agent.xml и JAR-файл должны быть в одной папке. Они часто расположены вместе в папке `/resources` проекта.  

### <a name="spring-rest-template"></a>Шаблон Spring Rest

Для того чтобы Application Insights успешно обрабатывал HTTP-вызовы, сделанные с помощью шаблона Spring Rest, необходимо использовать клиент HTTP Apache. По умолчанию шаблон Spring Rest не настроен для использования клиента HTTP Apache. Указав [ HttpComponentsClientHttpRequestfactory ](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/HttpComponentsClientHttpRequestFactory.html) в конструкторе шаблона Spring Rest, он будет использовать Apache HTTP.

Вот пример того, как это сделать с помощью Spring Beans. Это очень простой пример, использующий параметры по умолчанию для класса фабрики.

```java
@bean
public ClientHttpRequestFactory httpRequestFactory() {
return new HttpComponentsClientHttpRequestFactory()
}
@Bean(name = 'myRestTemplate')
public RestTemplate dcrAccessRestTemplate() {
    return new RestTemplate(httpRequestFactory())
}
```

#### <a name="enable-w3c-distributed-tracing"></a>Активация распределенной трассировки W3C

Добавьте к файлу AI-Agent.xml следующее:

```xml
<Instrumentation>
        <BuiltIn enabled="true">
            <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
        </BuiltIn>
    </Instrumentation>
```

> [!NOTE]
> Режим обратной совместимости включен по умолчанию, и параметр enableW3CBackCompat является необязательным и должен использоваться только в том случае, если вы хотите отключить эту функцию. 

В идеале он может использоваться, когда все ваши службы обновлены к новой версии пакетов SDK, поддерживающих протокол консорциума W3C. Настоятельно рекомендуется как можно скорее перейти к новой версии пакетов SDK с поддержкой W3C.

Убедитесь, что **оба [входящий](correlation.md#w3c-distributed-tracing) и исходящий (агенты) конфигурации** совпадают.

## <a name="view-the-data"></a>Просмотр данных
В ресурсе Application Insights сводные данные по удаленным зависимостям и времени выполнения методов отображаются в [элементе "Производительность"][metrics].

Для поиска отдельных экземпляров отчетов по зависимостям, исключениям и методам откройте [Поиск][diagnostic].

[Дополнительные сведения о диагностировании проблем зависимостей](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Вопросы? Проблемы?
* Данные отсутствуют? [Настройте исключения брандмауэра](../../azure-monitor/app/ip-addresses.md)
* [Устранение неполадок Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
