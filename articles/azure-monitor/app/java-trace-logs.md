---
title: Изучение журналов трассировки Java в Azure Application Insights
description: Поиск данных трассировки Log4J или Logback в Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/18/2019
ms.openlocfilehash: f552ccdbc67df93913c698e5d763dbb62b48a4ad
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927242"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Просмотр журналов трассировки Java в Application Insights
Если вы используете Logback или Log4J (версия 1.2 или 2.0) для трассировки, можно настроить автоматическую пересылку журналов в Application Insights, где вы сможете их изучить.

> [!TIP]
> Для приложения необходимо только установить ключ инструментирования Application Insights. Если вы используете такую платформу, как пружина Java, возможно, вы уже зарегистрировали ключ в конфигурации приложения.

## <a name="using-the-application-insights-java-agent"></a>Использование агента Application Insights Java

По умолчанию агент Application Insights Java автоматически отслеживает ведение журнала, выполненное на уровне `WARN` и выше.

Вы можете изменить пороговое значение ведения журнала, которое захватывается с помощью файла `AI-Agent.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

Вы можете отключить запись журнала агента Java с помощью файла `AI-Agent.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>Кроме того, в отличие от использования агента Java, можно выполнить приведенные ниже инструкции.

### <a name="install-the-java-sdk"></a>Установка пакета SDK для Java

Если вы еще не сделали этого, следуйте инструкциям по установке [пакета SDK для Application Insights для Java][java].

### <a name="add-logging-libraries-to-your-project"></a>Добавление в проект библиотеки ведения журналов
*Выберите подходящий метод для проекта.*

#### <a name="if-youre-using-maven"></a>Если вы используете Maven...
Если проект уже настроен для сборки с использованием Maven, добавьте один из следующих фрагментов кода в файл pom.xml.

Затем обновите зависимости проекта, чтобы загрузить двоичные файлы.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J версии 2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J версии 1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Если вы используете Gradle...
Если проект уже настроен для сборки с использованием Gradle, добавьте одну из следующих строк в группу `dependencies` в файле build.gradle.

Затем обновите зависимости проекта, чтобы загрузить двоичные файлы.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J версии 2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J версии 1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>В противном случае...
Следуйте указаниям, чтобы вручную установить пакет SDK Application Insights для Java, скачайте JAR-файл (после перехода на страницу центра Maven щелкните ссылку JAR в разделе загрузок) для соответствующего аппендера и добавьте скачанный JAR-файл аппендера в проект.

| Средство ведения журнала | Download (Скачать) | Библиотека |
| --- | --- | --- |
| Logback |[JAR-файл аппендера Logback](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J версии 2.0 |[JAR-файл аппендера Log4J версии 2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4J версии 1.2 |[JAR-файл аппендера Log4J версии 1.2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>Добавление аппендера в платформу ведения журнала
Чтобы начать трассировку, добавьте соответствующий фрагмент кода в файл конфигурации Log4J или Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J версии 2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J версии 1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

К аппендерам Application Insights может обращаться любое сконфигурированное средство ведения журнала, а не только корневое средство ведения журнала (как показано в примерах выше).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Просмотр данных трассировки на портале Application Insights
Теперь, когда проект настроен для отправки трассировок в Application Insights, можно просматривать и искать эти трассировки на портале Application Insights в колонке [Поиск][diagnostic] .

Исключения, отправленные с помощью средств ведения журнала, будут отображаться на портале в разделе телеметрии исключений.

![На портале Application Insights откройте колонку "Поиск".](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Дальнейшие действия
[Поиск по журналу диагностики][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


