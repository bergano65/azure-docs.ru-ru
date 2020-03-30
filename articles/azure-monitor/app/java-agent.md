---
title: Мониторинг производительности веб-приложений Java - Azure Application Insights
description: Расширенный мониторинг производительности и использования веб-сайта Java с помощью Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: b29618179d22eac97a07bf41906465aba1fd7929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657033"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Мониторинг зависимостей, пойманных исключений и времени выполнения методов в веб-приложениях Java


[Инструментирование веб-приложения Java с помощью Application Insights][java] позволяет получать более подробную информацию без изменения кода, используя для этого агент для Java.

* **Зависимости** — данные о вызовах других компонентов в вашем приложении, включая:
  * **Исходящие httpзвонки,** сделанные через Apache `java.net.HttpURLConnection` HttpClient, OkHttp, и захвачены.
  * **Звонки Редиса,** сделанные через клиента джедаев, захвачены.
  * **Запросы JDBC** - Для MyS'L и PostgreS'L, если вызов занимает более 10 секунд, агент сообщает план запроса.

* **Регистрация приложений:** Захват и корреляция журналов приложений с запросами HTTP и другой телеметрией
  * **Log4j 1.2**
  * **Log4j2**
  * **Вход**

* **Лучшее наименование операций:** (используется для агрегирования запросов на портале)
  * **Весна** - `@RequestMapping`на основе .
  * **JAX-RS** - `@Path`на основе . 

Чтобы использовать агент для Java, его необходимо установить на сервере. Веб-приложения необходимо инструментировать [пакетом SDK для Java Application Insights][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Установка агента Application Insights для Java
1. [Скачайте агент](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) на компьютер с сервером Java. Обязательно скачайте агент Java той же версии, что и веб-пакеты и пакет SDK для Java Application Insights.
2. Отснавите сценарий запуска сервера приложения и добавьте следующий аргумент JVM:
   
    `-javaagent:<full path to the agent JAR file>`
   
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
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Дополнительная конфигурация (Весенняя бутса)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Для служб приложений Azure сделайте следующее:

* Выберите элементы "Параметры > Параметры приложения".
* В разделе "Параметры приложения" добавьте новую пару "ключ — значение":

Ключ: `JAVA_OPTS` Значение:`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Для последней версии Java-агента, проверьте релизы [здесь](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

Агент должен быть упакован в качестве ресурса в вашем проекте таким образом, чтобы он оказался в D:/home/site/wwwroot/ каталоге. Вы можете подтвердить, что ваш агент находится в правильном каталоге Службы Приложений, перейдя на**консоль debug** **Advanced** > Tools Advanced**Tools** > и изучив содержимое каталога сайта.    

* Сохраните параметры и перезапустите приложение. (Эти действия применяются только к службам приложений, работающим на Windows.)

> [!NOTE]
> Файлы агента AI-Agent.xml и JAR-файл должны быть в одной папке. Они часто расположены вместе в папке `/resources` проекта.  

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

В идеале он может использоваться, когда все ваши службы обновлены к новой версии пакетов SDK, поддерживающих протокол консорциума W3C. Настоятельно рекомендуется перейти к новой версии пакетов SDK с поддержкой W3C как можно скорее.

Убедитесь, что **оба [входящий](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) и исходящий (агенты) конфигурации** совпадают.

## <a name="view-the-data"></a>Просмотр данных
В ресурсе Application Insights агрегированные удаленные зависимости и время выполнения метода отображаются [под плиткой производительности.][metrics]

Для поиска отдельных экземпляров отчетов по зависимостям, исключениям и методам откройте [Поиск][diagnostic].

[Дополнительные сведения о диагностировании проблем зависимостей](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Вопросы? Проблемы?
* Данные отсутствуют? [Настройка исключений брандмауэра](../../azure-monitor/app/ip-addresses.md)
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
