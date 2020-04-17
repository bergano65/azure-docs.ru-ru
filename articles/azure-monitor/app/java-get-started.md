---
title: 'Быстрый запуск: аналитика веб-приложений Java с Azure Application Insights'
description: 'Сведения о мониторинге производительности веб-приложений Java с помощью Application Insights. '
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: e56ba304d197984110de5127a0f163ac0accf1aa
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537514"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Быстрый запуск: Начните с Application Insights в веб-проекте Java

В этом быстром запуске вы используете Application Insights для автоматического запроса на инструментирование, отслеживания зависимостей и сбора счетчиков производительности, диагностики проблем с производительностью и исключений и записи кода для отслеживания того, что пользователи делают с вашим приложением.

Application Insights — это расширяемая служба аналитики для разработчиков веб-ресурсов, позволяющая оценивать производительность и использование работающего приложения. Надстройка Application Insights поддерживает приложения Java, работающие под управлением Linux, Unix или Windows.

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.
* Функционирующее java-приложение.

## <a name="get-an-application-insights-instrumentation-key"></a>Получение ключа инструментирования Application Insights

1. Войдите на [портал Azure](https://portal.azure.com/).
2. На портале Azureсоздайте ресурс Application Insights. Задайте тип приложения: веб-приложение Java.

3. Найдите ключ инструментирования нового ресурса. Далее будет необходимо вставить его в проект кода.

    ![В обзоре нового ресурса щелкните "Свойства" и скопируйте ключ инструментирования](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>Добавление в проект пакета SDK Application Insights для Java

*Выберите тип проекта.*

# <a name="maven"></a>[Maven](#tab/maven)

Если ваш проект уже настроен на использование Maven для сборки, объедините следующий код в файл *pom.xml.*

Затем обновите зависимости проекта, чтобы скачать двоичные файлы.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Если проект уже настроен на использование Gradle для сборки, объедините следующий код в файл *build.gradle.*

Затем обновите зависимости проекта, чтобы скачать двоичные файлы.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

# <a name="other-types"></a>[Другие типы](#tab/other)

Скачайте [последнюю версию](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) и скопируйте необходимые файлы в проект, заменив все предыдущие версии.

---

### <a name="questions"></a>Вопросы
* *Какова связь между `-web-auto`, `-web` и `-core` компоненты?*
  * `applicationinsights-web-auto`дает метрики, которые отслеживают количество запросов на сервет HTTP и время отклика, автоматически регистрируя фильтр сервопривода Application Insights во время выполнения.
  * `applicationinsights-web`также дает вам метрики, которые отслеживают количество запросов на сервет HTTP и время ответа, но требует ручной регистрации фильтра сервопривода Application Insights в приложении.
  * `applicationinsights-core`дает только голый API, например, если ваше приложение не основано на сервете.
  
* *Как обновить пакет SDK до последней версии?*
  * Если вы используете Gradle или Maven...
    * Обновите файл сборки, чтобы указать последнюю версию.
  * Если вы вручную управляете зависимостями...
    * Загрузите последнюю версию [пакета SDK Application Insights для Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) и установите ее вместо более старых версий. Изменения описаны в статье [Заметки о выпуске пакета SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="add-an-applicationinsightsxml-file"></a>Добавить файл *ApplicationInsights.xml*
Добавьте *ApplicationInsights.xml* в папку ресурсов в проекте или убедитесь, что она добавлена в путь класса развертывания проекта. Скопируйте в него следующий код XML.

Замените ключ приборов тем, который вы получили с портала Azure.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

   <!-- The key from the portal: -->
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

   <!-- HTTP request component (not required for bare API) -->
   <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   </TelemetryModules>

   <!-- Events correlation (not required for bare API) -->
   <!-- These initializers add context data to each event -->
   <TelemetryInitializers>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

По желанию файл конфигурации может находиться в любом месте, доступном для приложения.  Свойство `-Dapplicationinsights.configurationDirectory` системы определяет каталог, содержащий *ApplicationInsights.xml.* Например, файл конфигурации, расположенный в каталоге `E:\myconfigs\appinsights\ApplicationInsights.xml`, будет настроен со свойством `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* Ключ инструментирования пересылается вместе с каждым элементом телеметрии; служба Application Insights отобразит его в ресурсе.
* Компонент HTTP-запросов является необязательным. Он автоматически передает на портал телеметрию о запросах и значения времени ответа.
* Корреляция события является дополнением к компоненту HTTP-запросов. Он присваивает идентификатор каждому запросу, полученному сервером. Затем он добавляет этот идентификатор в качестве свойства к каждому элементу телеметрии в качестве свойства "Operation.Id". Благодаря этому можно выделить данные телеметрии, связанные с каждым из запросов, путем установки фильтра [Поиск по журналу диагностики][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Другие способы задать ключ инструментирования
Пакет SDK Application Insights ищет ключ в следующем порядке:

1. Свойство системы: -DAPPINSIGHTS_INSTRUMENTATIONKEY-your_ikey
2. Переменная среда: APPINSIGHTS_INSTRUMENTATIONKEY
3. Файл конфигурации: *ApplicationInsights.xml*

Вы также можете [задать его в коде](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Добавление агента

[Установите Java-агент](java-agent.md) для захвата исходящих вызовов HTTP, запросов JDBC, регистрации приложений и лучшего именования операций.

## <a name="run-your-application"></a>Запуск приложения
Запустите приложение в режиме отладки на компьютере разработки или опубликуйте его на своем сервере.

## <a name="view-your-telemetry-in-application-insights"></a>Просмотр данных телеметрии в Application Insights
Вернитесь к ресурсу Application Insights на [портале Microsoft Azure](https://portal.azure.com).

В колонке обзора появятся данные HTTP-запросов. (Если данные отсутствуют, подождите несколько секунд и нажмите кнопку обновления).

![Снимок экрана: обзор с примером данных](./media/java-get-started/overview-graphs.png)

[Подробнее о метриках.][metrics]

Щелкните любую диаграмму, чтобы увидеть более подробные агрегированные метрики.

![Панель сбоев Application Insights с диаграммами](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Данные экземпляров
Щелкните тип запроса, чтобы просмотреть отдельные экземпляры.

![Просверлить в определенном представлении образца](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Аналитика: мощный язык запросов
По мере увеличения объема накопленных данных вы сможете использовать запросы для объедения данных и поиска отдельных экземпляров.  [Аналитика](../../azure-monitor/app/analytics.md) — это мощный инструмент, который не только позволяет изучать сведения о производительности и использовании, но и диагностировать возможные неполадки.

![Пример аналитики](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Установка приложения на сервере
Теперь опубликуйте приложение на сервере, откройте доступ для пользователей и изучайте телеметрию на портале.

* Убедитесь, что брандмауэр позволяет приложению отправлять телеметрию на следующие порты:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Если исходящий трафик должен проходить через брандмауэр, определите системные свойства `http.proxyHost` и `http.proxyPort`.

* На серверах Windows необходимо установить следующее:

  * [распространяемые компоненты Microsoft Visual C++.](https://www.microsoft.com/download/details.aspx?id=40784)

    (Сюда входят счетчики производительности).

## <a name="azure-app-service-config-spring-boot"></a>Конфигурация сервиса приложений Azure (Весенняя загрузка)

Приложения Spring Boot, работающие на Windows, требуют дополнительной конфигурации для работы в службах приложений Azure. Измените **web.config** и добавьте следующую конфигурацию:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Исключения и ошибки запросов
Необработанные исключения и сбои запроса автоматически собираются веб-фильтром Application Insights.

Для сбора данных о других исключениях можно [вставить вызовы для отслеживания() в коде.][apiexceptions]

## <a name="monitor-method-calls-and-external-dependencies"></a>Мониторинг вызовов методов и внешних зависимостей.
[установить агент для Java](java-agent.md) .

И для автоматического именования операции.

## <a name="w3c-distributed-tracing"></a>Распределенная трассировка W3C

Пакет средств разработки для Java Application Insights теперь поддерживает [распределенную трассировку W3C](https://w3c.github.io/trace-context/).

Конфигурация входящих параметров пакета средств разработки описана далее в статье о [корреляции](correlation.md).

Конфигурация входящих параметров пакета средств разработки задана в файле [AI-Agent.xml](java-agent.md).

## <a name="performance-counters"></a>Счетчики производительности
Открыть **Исследовать**, **Метрики**, чтобы увидеть диапазон счетчиков производительности.

![Скриншот панели метрик с выбранными частными байтами процесса](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Настройка сбора данных счетчиками производительности
Чтобы отключить коллекцию стандартных счетчиков производительности, добавьте следующий код под корневым узлафайлом файла *ApplicationInsights.xml:*

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Сбор данных дополнительными счетчиками производительности
Можно задать необходимость сбора данных дополнительными счетчиками производительности.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Счетчики JMX (предоставляются виртуальной машиной Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` — имя, отображаемое в портале Application Insights.
* `objectName` — имя объекта JMX.
* `attribute` — атрибут имени объекта JMX для выборки
* `type`(необязательно) - Тип атрибута объекта JMX:
  * по умолчанию: простой тип, такой как int или long;
  * `composite`— данные счетчика производительности имеют формат "Атрибут.Данные";
  * `tabular`— данные счетчика производительности имеют формат строки таблицы.

#### <a name="windows-performance-counters"></a>Счетчики производительности Windows
Каждый [счетчик производительности Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) входит в состав категории (аналогично поле является членом класса). Категория может быть глобальной либо иметь пронумерованные или именованные экземпляры.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName — имя, отображаемое в портале Application Insights.
* categoryName — категория счетчика производительности (объект производительности), с которой связан этот счетчик производительности.
* counterName — имя счетчика производительности.
* instanceName — имя экземпляра категории счетчика производительности или пустая строка (""), если категория содержит единственный экземпляр. Если categoryName имеет значение "Process", и источником данных для счетчиков производительности является текущий процесс виртуальной машины Java, на которой выполняется ваше приложение, укажите `"__SELF__"`.

### <a name="unix-performance-counters"></a>Счетчики производительности Unix
* [установите collectd с подключаемым модулем Application Insights](java-collectd.md) .

## <a name="get-user-and-session-data"></a>Получение данных о пользователях и сеансах
Итак, вы отправляете телеметрию с веб-сервера. Теперь для получения полного представления о приложении можно настроить дополнительные функции мониторинга:

* [Добавьте телеметрии на веб-страницы][usage] для мониторинга просмотров страниц и метрик пользователя.
* [Настройте веб-тесты][availability], которые помогут быть уверенными в том, что приложение остается работоспособным и правильно отвечает на запросы.

## <a name="send-your-own-telemetry"></a>Отправка собственных данных телеметрии
После установки пакета SDK можно использовать интерфейс API для отправки собственных данных телеметрии.

* [Отслеживайте пользовательские события и метрики][api], чтобы знать, какие операции выполняют пользователи в приложении.
* [Выполняйте поиск событий и журналов][diagnostic] для диагностики неполадок.

## <a name="availability-web-tests"></a>Доступность веб-тестов
Application Insights может тестировать ваш веб-сайт через равные промежутки времени для проверки, работает ли он и правильно ли отвечает на запросы.

[Узнайте больше о том, как настроить веб-тесты доступности.][availability]

## <a name="questions-problems"></a>Вопросы? Проблемы?
[Устранение неполадок Java](java-troubleshoot.md)

## <a name="next-steps"></a>Следующие шаги
* [Отслеживайте вызовы зависимостей.](java-agent.md)
* [Отслеживайте счетчики производительности Unix.](java-collectd.md)
* Добавляйте [мониторинг на веб-страницы](javascript.md), чтобы отслеживать время загрузки страниц, вызовы AJAX и исключения браузера.
* Пишите [пользовательскую телеметрию](../../azure-monitor/app/api-custom-events-metrics.md) для отслеживания использования в браузере или на сервере.
* Используйте [Analytics](../../azure-monitor/app/analytics.md) для мощных запросов по телеметрии из вашего приложения
* Дополнительные сведения см. в разделе [Azure for Java developers](/java/azure) (Azure для разработчиков Java).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[usage]: javascript.md
