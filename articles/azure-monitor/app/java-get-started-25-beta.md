---
title: Анализ веб-приложений Java с помощью Azure Application Insights (2.5.0-BETA) | Документация Майкрософт
description: 'Мониторинг производительности приложений для веб-приложений Java с помощью Application Insights (2.5.0-BETA). '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: lagayhar
ms.openlocfilehash: 17ad99d372bbca1d82c5c3701751da1b009764ee
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967839"
---
# <a name="get-started-with-application-insights-in-a-java-web-project-250-beta"></a>Начало работы с Application Insights в веб-проекте Java (2.5.0-BETA)

[Application Insights](https://azure.microsoft.com/services/application-insights/) — это расширяемая служба аналитики для разработчиков веб-ресурсов, позволяющая оценивать производительность и использование работающего приложения. Используйте его для [автоматического инструментирования запросов, мониторинга зависимостей и сбора счетчиков производительности](auto-collect-dependencies.md#java), диагностики проблем производительности и исключений, а также для [написания кода][api] , который позволяет отслеживать действия пользователей с приложением. 

![Снимок экрана: обзор с примером данных](./media/java-get-started/overview-graphs.png)

Надстройка Application Insights поддерживает приложения Java, работающие под управлением Linux, Unix или Windows.

Вам необходимы:

* Java 7 или более поздней версии
* подписка на [Microsoft Azure](https://azure.microsoft.com/).

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Получение ключа инструментирования Application Insights
1. Войдите на [портал Microsoft Azure](https://portal.azure.com).
2. Создайте ресурс Application Insights. Задайте тип приложения: веб-приложение Java.

3. Найдите ключ инструментирования нового ресурса. Далее будет необходимо вставить его в проект кода.

    ![В обзоре нового ресурса щелкните "Свойства" и скопируйте ключ инструментирования](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Добавление в проект пакета SDK Application Insights для Java
*Выберите подходящий метод для проекта.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Если вы используете Maven<a name="maven-setup" />
Если проект уже настроен для сборки с использованием Maven, добавьте следующий код в файл pom.xml.

Затем обновите зависимости проекта, чтобы скачать двоичные файлы.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0-BETA</version>
      </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Если вы используете Gradle<a name="gradle-setup" />
Если проект уже настроен для сборки с использованием Gradle, добавьте следующий фрагмент кода в файл build.gradle.

Затем обновите зависимости проекта, чтобы скачать двоичные файлы.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0-BETA'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Если вы вручную управляете зависимостями
Скачайте [последнюю версию](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) и скопируйте необходимые файлы в проект, заменив все предыдущие версии.

### <a name="questions"></a>Вопросы
* *Какова связь между `-web-auto` `-web` компонентами и `-core` ?*
  * `applicationinsights-web-auto`предоставляет метрики, которые отсчитываются количество запросов HTTP сервлета и время отклика путем автоматической регистрации фильтра Application Insights сервлета в среде выполнения.
  * `applicationinsights-web`также предоставляет метрики, которые отсчитываются количество запросов HTTP сервлета и время ответа, но требуют ручной регистрации фильтра Application Insights сервлета в приложении.
  * `applicationinsights-core`предоставляет только простой API, например, если приложение не основано на сервлета.
  
* *Как обновить пакет SDK до последней версии?*
  * Если вы используете Gradle или Maven
    * Обновите файл сборки, чтобы указать последнюю версию.
  * Если вы вручную управляете зависимостями
    * Загрузите последнюю версию [пакета SDK Application Insights для Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) и установите ее вместо более старых версий. Изменения описаны в статье [Заметки о выпуске пакета SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. Добавление файла ApplicationInsights.xml
Добавьте ApplicationInsights.xml в папку ресурсов проекта или проверьте, добавлен ли этот файл в путь класса развертывания проекта. Скопируйте в него следующий код XML.

Замените ключ инструментирования на полученный в портале Azure.

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

При необходимости файл конфигурации может находиться в любом месте, доступном для приложения.  Системное свойство `-Dapplicationinsights.configurationDirectory` указывает на каталог, содержащий файл ApplicationInsights.xml. Например, файл конфигурации, расположенный в каталоге `E:\myconfigs\appinsights\ApplicationInsights.xml`, будет настроен со свойством `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* Ключ инструментирования пересылается вместе с каждым элементом телеметрии; служба Application Insights отобразит его в ресурсе.
* Компонент HTTP-запросов является необязательным. Он автоматически передает на портал телеметрию о запросах и значения времени ответа.
* Корреляция события является дополнением к компоненту HTTP-запросов. Это дополнение назначает идентификатор для каждого запроса, полученного сервером, и добавляет его в качестве свойства каждого элемента телеметрии в форме "Операция.ИД". Благодаря этому можно выделить данные телеметрии, связанные с каждым из запросов, путем установки фильтра [Поиск по журналу диагностики][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Другие способы задать ключ инструментирования
Пакет SDK Application Insights ищет ключ в следующем порядке:

1. Системное свойство:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Переменная среды. APPINSIGHTS_INSTRUMENTATIONKEY
3. Файл конфигурации. ApplicationInsights.xml

Вы также можете [задать его в коде](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-agent"></a>4. Добавить агент

[Установите агент Java](java-agent-25-beta.md) для отслеживания исходящих вызовов HTTP, запросов JDBC, ведения журнала приложений и лучшего именования операций.

## <a name="5-run-your-application"></a>5. Запуск приложения
Запустите приложение в режиме отладки на компьютере разработки или опубликуйте его на своем сервере.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Просмотр данных телеметрии в Application Insights
Вернитесь к ресурсу Application Insights на [портале Microsoft Azure](https://portal.azure.com).

В колонке обзора появятся данные HTTP-запросов. (Если данные отсутствуют, подождите несколько секунд и нажмите кнопку обновления).

![Снимок экрана: обзор с примером данных](./media/java-get-started/overview-graphs.png)

[Дополнительные сведения о метриках.][metrics]

Щелкните любую диаграмму, чтобы увидеть более подробные агрегированные метрики.

![Панель Application Insights сбоев с диаграммами](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0-BETA operation naming provided by agent]
-->

### <a name="instance-data"></a>Данные экземпляров
Щелкните тип запроса, чтобы просмотреть отдельные экземпляры.

![Детализация конкретного примера представления](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Аналитика. Эффективный язык запросов
По мере увеличения объема накопленных данных вы сможете использовать запросы для объедения данных и поиска отдельных экземпляров.  [Аналитика](../../azure-monitor/app/analytics.md) — это мощный инструмент, который не только позволяет изучать сведения о производительности и использовании, но и диагностировать возможные неполадки.

![Пример аналитики](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Установка приложения на сервере
Теперь опубликуйте приложение на сервере, откройте доступ для пользователей и изучайте телеметрию на портале.

* Убедитесь, что брандмауэр позволяет приложению отправлять телеметрию на следующие порты:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Если исходящий трафик должен проходить через брандмауэр, определите системные свойства `http.proxyHost` и `http.proxyPort`.

* На серверах Windows необходимо установить следующее:

  * [распространяемые компоненты Microsoft Visual C++.](https://www.microsoft.com/download/details.aspx?id=40784)

    (Сюда входят счетчики производительности).

## <a name="azure-app-service-config-spring-boot"></a>Конфигурация службы приложений Azure (пружинная загрузка)

Приложения с пружинной загрузкой, запущенные в Windows, нуждаются в дополнительной настройке для запуска в службах приложений Azure. Измените **файл Web. config** и добавьте следующее:

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
Необработанные исключения и ошибки запросов автоматически собираются Application Insights веб-фильтром.

Чтобы получить данные о других исключениях, можно [Вставить в код вызовы trackException ()][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Мониторинг вызовов методов и внешних зависимостей.
[установить агент для Java](java-agent.md) .

И для автоматического именования операций.

## <a name="w3c-distributed-tracing"></a>Распределенная трассировка W3C

Пакет средств разработки для Java Application Insights теперь поддерживает [распределенную трассировку W3C](https://w3c.github.io/trace-context/).

Конфигурация входящих параметров пакета средств разработки описана далее в статье о [корреляции](correlation.md#w3c-distributed-tracing).

Конфигурация входящих параметров пакета средств разработки задана в файле [AI-Agent.xml](java-agent.md).

## <a name="performance-counters"></a>Счетчики производительности
Откройте раздел **исследование**, **метрики**, чтобы просмотреть диапазон счетчиков производительности.

![Снимок экрана: панель метрик с выбранным байтовым частным байтом процесса](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Настройка сбора данных счетчиками производительности
Чтобы отключить сбор данных стандартным набором счетчиков производительности, добавьте следующий фрагмент кода в корневой узел файла ApplicationInsights.xml:

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
* `type` (необязательно) — тип атрибута объекта JMX:
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
* [Настройте веб-тесты][availability] , которые помогут быть уверенными в том, что приложение остается работоспособным и правильно отвечает на запросы.

## <a name="send-your-own-telemetry"></a>Отправка собственных данных телеметрии
После установки пакета SDK можно использовать интерфейс API для отправки собственных данных телеметрии.

* [Следите за пользовательскими событиями и метриками][api] , чтобы узнать, какие пользователи выполняются с приложением.
* [Поиск событий и журналов][diagnostic] для диагностики неполадок.

## <a name="availability-web-tests"></a>Доступность веб-тестов
Application Insights может тестировать ваш веб-сайт через равные промежутки времени для проверки, работает ли он и правильно ли отвечает на запросы.

[Дополнительные сведения о настройке веб-тестов доступности см. здесь.][availability]

## <a name="questions-problems"></a>Есть вопрос? Проблемы?
[Устранение неполадок Java](java-troubleshoot.md)

## <a name="next-steps"></a>Следующие шаги
* [Отслеживайте вызовы зависимостей.](java-agent.md)
* [Отслеживайте счетчики производительности Unix.](java-collectd.md)
* Добавляйте [мониторинг на веб-страницы](javascript.md), чтобы отслеживать время загрузки страниц, вызовы AJAX и исключения браузера.
* Пишите [пользовательскую телеметрию](../../azure-monitor/app/api-custom-events-metrics.md) для отслеживания использования в браузере или на сервере.
* Используйте [аналитику](../../azure-monitor/app/analytics.md), чтобы выполнять эффективные запросы телеметрии из приложения.
* Дополнительные сведения см. в разделе [Azure for Java developers](/java/azure) (Azure для разработчиков Java).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs-25-beta.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
