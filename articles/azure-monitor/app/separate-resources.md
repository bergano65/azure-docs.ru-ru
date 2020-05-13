---
title: Как спроектировать развертывание Application Insights — один или несколько ресурсов?
description: Отправка телеметрии к различным ресурсам для меток разработки, тестирования и эксплуатации.
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 6df6622cbba251c221533c3307dc194f08e871fb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125695"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>Сколько Application Insights ресурсов следует развернуть

Разрабатывая следующую версию веб-приложения, вы не хотели бы путать данные телеметрии [Application Insights](../../azure-monitor/app/app-insights-overview.md), полученные для новой версии, с данными уже выпущенной. Чтобы избежать путаницы, отправляйте данные телеметрии разных стадий разработки в отдельные ресурсы Application Insights, используя отдельные ключи инструментирования. Чтобы облегчить изменение ключа инструментирования в зависимости от стадии разработки, вы можете задать ключи инструментирования в коде, а не в файле конфигурации.

(Если вы используете облачную службу Azure, есть [другой метод настройки отдельных ключей инструментирования ikey](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Ресурсы и ключи инструментирования

При настройке мониторинга Application Insights для веб-приложения вы создаете *ресурс* Application Insights в Microsoft Azure. Вы открываете этот ресурс на портале Azure для просмотра и анализа телеметрии, собранной из приложения. Каждый ресурс идентифицируется с помощью *ключа инструментирования* (ikey). При установке пакета Application Insights для мониторинга приложения вы можете настроить для него ключ инструментирования. Так ему будет известно, куда отправлять телеметрию.

Каждый ресурс Application Insights поставляется с метриками, которые доступны по раскрывающемся списке. Если отчет о компонентах полностью разделяются на один и тот же ресурс Application Insights, эти метрики могут не иметь смысла для панели мониторинга или оповещения.

### <a name="when-to-use-a-single-application-insights-resource"></a>Когда следует использовать один ресурс Application Insights

-   Для компонентов приложения, которые развертываются вместе. Обычно разрабатывается одной командой, управляемой одним и тем же набором пользователей DevOps и Итопс.
-   Если имеет смысл агрегировать ключевые показатели эффективности (KPI), такие как длительность ответа, количество сбоев на панели мониторинга и т. д., по умолчанию (можно выбрать сегментирование по имени роли в обозреватель метрик интерфейсе).
-   Если управление доступом на основе ролей (RBAC) не требуется для разных компонентов приложения.
-   Если не требуются критерии оповещения метрик, которые отличаются между компонентами.
-   Если не требуется управлять непрерывными экспортами по-разному между компонентами.
-   Если вам не нужно управлять выставлением счетов и квотами по-разному между компонентами.
-   Если у вас нет доступа к данным из всех компонентов, ключ API будет иметь такой же доступ. И 10 ключей API достаточно для всех задач.
-   Если для всех ролей требуется одинаковое интеллектуальное обнаружение и параметры интеграции рабочих элементов.

### <a name="other-things-to-keep-in-mind"></a>Другие вещи, которые следует учитывать

-   Может потребоваться добавить пользовательский код, чтобы обеспечить установку значимых значений в атрибут [Cloud_RoleName](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net#set-cloud-role-name) . Если для этого атрибута не заданы значимые значения, *ни один* из возможностей портала не будет работать.
- Для Service Fabric приложений и классических облачных служб пакет SDK автоматически считывает данные из среды роли Azure и устанавливает их. Для всех других типов приложений, скорее всего, потребуется задать его явным образом.
-   Интерактивный интерфейс метрик не поддерживает разбиение по имени роли.

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Динамический ключ инструментирования

Чтобы упростить изменение ключа инструментирования при перемещении кода между этапами производства, задавайте его в коде, а не в файле конфигурации.

Задайте ключ в методе инициализации, таком как global.aspx.cs, в службе ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

В этом примере ключи инструментирования (ikey) для различных ресурсов приводятся в различных версиях файла веб-конфигурации. При смене файла веб-конфигурации, что можно сделать в рамках сценария выпуска, сменится и целевой ресурс.

### <a name="web-pages"></a>Веб-страницы
IKey также используется на веб-страницах вашего приложения в [сценарии, полученном из панели](../../azure-monitor/app/javascript.md)быстрого запуска. Вместо того чтобы вставлять его в код сценария напрямую, генерируйте его из состояния сервера. Например, в приложении ASP.NET:

*JavaScript в Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Создание дополнительных ресурсов Application Insights

Чтобы создать ресурс Application Insights, следуйте руководству по [созданию ресурсов](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

### <a name="getting-the-instrumentation-key"></a>Получение ключа инструментирования
Ключ инструментирования идентифицирует созданный вами ресурс.

Вам потребуются ключи инструментирования всех ресурсов, в которые приложение будет отправлять данные.

## <a name="filter-on-build-number"></a>Фильтрация по номеру сборки
При публикации новой версии приложения имеет смысл отделить телеметрию от других сборок.

Для этого можно настроить свойство "Версия приложения" для фильтрации результатов [поиска](../../azure-monitor/app/diagnostic-search.md) и [обозревателя метрик](../../azure-monitor/platform/metrics-charts.md).

Свойство "Версия приложения" можно настроить различными способами.

* Напрямую:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Вставьте эту строку в [инициализатор телеметрии](../../azure-monitor/app/api-custom-events-metrics.md#defaults) , чтобы обеспечить согласованность всех экземпляров TelemetryClient.
* [ASP.NET] Задайте версию в `BuildInfo.config`. Веб-модуль берет номер версии из узла BuildLabel. Включите этот файл в проект и не забудьте установить свойство «Всегда копировать» в обозревателе решений.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Настройте автоматическое создание файла BuildInfo.config в MSBuild. Для этого добавьте в файл `.csproj` несколько строк:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    При этом создается файл с именем *yourprojectname собственным значением*. BuildInfo. config. процесс публикации переименовывает его в файл BuildInfo. config.

    При создании сборки с помощью Visual Studio в подпись включается заполнитель (AutoGen_...). Если используется MSBuild, в подписи указывается правильный номер версии.

    Чтобы разрешить MSBuild генерировать номера версий, задайте версию вида `1.0.*` в файле AssemblyReference.cs.

## <a name="version-and-release-tracking"></a>Отслеживание версии и выпуска
Для отслеживания версии приложения убедитесь, что во время выполнения процесса Microsoft Build Engine создается `buildinfo.config`. В `.csproj` файле добавьте:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

При наличии данных сборки веб-модуль Application Insights автоматически добавляет **версию приложения** как свойство для каждого элемента телеметрии. Это позволяет применить фильтр по версии при [диагностическом поиске](../../azure-monitor/app/diagnostic-search.md) или [изучении метрик](../../azure-monitor/platform/metrics-charts.md).

Однако обратите внимание, что номер версии сборки создается только Microsoft Build Engine, а не сборкой разработчика из Visual Studio.

### <a name="release-annotations"></a>Примечания к выпуску
Если используется Azure DevOps, можно настроить [добавление маркера заметки](../../azure-monitor/app/annotations.md) к диаграммам при выпуске новой версии. На следующем рисунке показано, как появляется этот маркер.

## <a name="next-steps"></a>Дальнейшие действия

* [Monitor multi-component applications with Application Insights (preview)](../../azure-monitor/app/app-map.md) (Мониторинг приложений с несколькими компонентами с помощью Application Insights (предварительная версия))
* [Добавление свойств: ITelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
