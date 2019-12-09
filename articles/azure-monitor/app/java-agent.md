---
title: Мониторинг производительности веб-приложений Java — Azure Application Insights
description: Расширенный мониторинг производительности и использования веб-сайта Java с помощью Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/10/2019
ms.openlocfilehash: 8194c4b16d114be9b2b95ff56dea59d98cfdae10
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931142"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Мониторинг зависимостей, перехваченных исключений и времени выполнения методов в веб-приложениях Java


Если вы выполнили [Инструментирование веб-приложения Java с Application Insights][java], вы можете использовать агент Java для получения более подробных сведений, не внося изменения в код:

* **Зависимости** — данные о вызовах других компонентов в вашем приложении, включая:
  * Регистрируются **исходящие HTTP-вызовы** через Apache HttpClient, OkHttp и `java.net.HttpURLConnection`.
  * **Вызовы Redis** , выполненные через клиент Jedis, фиксируются.
  * **Запросы JDBC** — для MySQL и PostgreSQL, если вызов занимает больше 10 секунд, агент сообщает о плане запроса.

* **Ведение журнала приложения:** Запись и сопоставление журналов приложений с запросами HTTP и другими данными телеметрии
  * **Log4j 1,2**
  * **Log4j2**
  * **Logback**

* **Улучшенное именование операций:** (используется для агрегирования запросов на портале)
  * **Пружина** на основе `@RequestMapping`.
  * **JAX-RS** — на основе `@Path`. 

Чтобы использовать агент для Java, его необходимо установить на сервере. Веб-приложения должны быть инструментированы с помощью [пакета SDK для Application Insights Java][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Установка агента Application Insights для Java
1. [Скачайте агент](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) на компьютер с сервером Java. Обязательно скачайте агент Java той же версии, что и веб-пакеты и пакет SDK для Java Application Insights.
2. Измените скрипт запуска сервера приложений и добавьте следующий аргумент ВИРТУАЛЬНОЙ машины Java:
   
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

## <a name="additional-config-spring-boot"></a>Дополнительная конфигурация (пружинная загрузка)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Для служб приложений Azure выполните следующие действия.

* Выберите элементы "Параметры > Параметры приложения".
* В разделе "Параметры приложения" добавьте новую пару "ключ — значение":

Ключ: `JAVA_OPTS` значение: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Для получения последней версии агента Java ознакомьтесь с выпусками [здесь](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

Агент должен быть упакован в проект в виде ресурса таким образом, чтобы он закончится в папке D:/Home, site/wwwroot/. Вы можете убедиться, что агент находится в нужном каталоге службы приложений, перейдя к **средствам разработки** > **дополнительные инструменты** > **консоль отладки** и проверив содержимое каталога сайта.    

* Сохраните параметры и перезапустите приложение. (Эти действия применимы только к службам приложений, работающим в Windows.)

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

В идеале он может использоваться, когда все ваши службы обновлены к новой версии пакетов SDK, поддерживающих протокол консорциума W3C. Настоятельно рекомендуется как можно скорее перейти к новой версии пакетов SDK с поддержкой W3C.

Убедитесь, что **оба [входящий](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) и исходящий (агенты) конфигурации** совпадают.

## <a name="view-the-data"></a>Просмотр данных
В ресурсе Application Insights агрегированная Удаленная зависимость и время выполнения метода отображаются [под плиткой "производительность"][metrics].

Для поиска отдельных экземпляров отчетов зависимости, исключений и методов откройте окно [поиска][diagnostic].

[Дополнительные сведения о диагностировании проблем зависимостей](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Есть вопросы? Проблемы?
* Нет данных? [Настройка исключений брандмауэра](../../azure-monitor/app/ip-addresses.md)
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
