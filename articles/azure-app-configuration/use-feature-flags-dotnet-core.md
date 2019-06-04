---
title: Руководство по использованию флагов функций в приложении .NET Core | Документация Майкрософт
description: В этом руководстве вы узнаете, как реализовать флаги функций в приложениях .NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: b0e48a0db63eded9e9c4921d33b03af39656ce0d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299265"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>Руководство по использованию флагов функций в приложении .NET Core

Библиотеки управления функциями .NET Core обеспечивают идиоматическую поддержку реализации флагов функций в приложении .NET или ASP.NET Core. Они позволяют добавлять флаги функций в код более декларативно, чтобы вам не пришлось записывать все операторы `if` для них вручную. Они в фоновом режиме управляют жизненными циклами флагов функций (например, обновляют и кэшируют состояния флагов, гарантируют неизменность состояния флага во время вызова запроса). Кроме того, библиотека ASP.NET Core предлагает готовое решение по интеграции, включая действия контроллера MVC, представления, маршруты и ПО промежуточного слоя.

В [этом кратком руководстве](./quickstart-feature-flag-aspnet-core.md) показано несколько способов добавления флагов функций в приложение ASP.NET Core. В этом руководстве они рассматриваются более подробно. Полную справочную документацию см. в [документации по управлению функциями ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410).

Из этого учебника вы узнаете следующее:

> [!div class="checklist"]
> * Добавить флаги функций в ключевых частях приложения для управления доступностью функций.
> * Выполнить интеграцию с Конфигурацией приложений при использовании этой службы для управления флагами функций.

## <a name="setup"></a>Настройка

Диспетчер функций .NET Core `IFeatureManager` возвращает флаги функций из собственной системы конфигурации платформы. Таким образом, вы можете определить флаги функций приложения, используя любой источник конфигурации, поддерживаемый .NET Core, включая локальный файл *appsettings.json* или переменные среды. В диспетчере функций используется внедрение зависимостей .NET Core. Вы можете зарегистрировать службы управления функциями с помощью стандартных соглашений.

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Диспетчер функций по умолчанию извлекает флаги функций из раздела FeatureManagement данных конфигурации .NET Core. В следующем примере данные считываются из другого раздела под названием MyFeatureFlags.

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

При использовании фильтров в флагах функций вам необходимо включить дополнительную библиотеку и зарегистрировать ее. В приведенном ниже примере показано, как использовать встроенный фильтр функций под названием **PercentageFilter**.

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Для эффективной работы следует хранить флаги функций вне приложения и управлять ими отдельно. Так вы можете изменять состояния флагов в любое время и немедленно применять эти изменения в приложении. Конфигурация приложений предоставляет централизованное расположение для организации и администрирования всех флагов функций через выделенный пользовательский интерфейс портала и доставляет флаги прямо в ваше приложение через клиентские библиотеки .NET Core. Самый простой способ подключить приложение ASP.NET Core к Конфигурации приложений — использовать поставщика конфигураций `Microsoft.Extensions.Configuration.AzureAppConfiguration`. Этот пакет NuGet можно использовать в коде, добавив следующий код в файл *Program.cs*:

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
           .ConfigureAppConfiguration((hostingContext, config) => {
               var settings = config.Build();
               config.AddAzureAppConfiguration(options => {
                   options.Connect(settings["ConnectionStrings:AppConfig"])
                          .UseFeatureFlags();
                });
           })
           .UseStartup<Startup>();
```

Значения флагов функций должны изменяться со временем. По умолчанию диспетчер функций будет обновлять значения флагов функций каждые 30 секунд. Вы можете использовать другой интервал опроса в вызове `options.UseFeatureFlags()`, приведенном выше.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Объявление флага функции

Каждый флаг функции состоит из двух частей: имени и списка из одного или нескольких фильтров, использующихся, чтобы оценить, *включена* ли функция (то есть, если его значение равно `True`). Фильтр определяет вариант использования, для которого должна быть включена функция. Если флаг функции имеет несколько фильтров, список фильтров проходится по порядку, пока один из фильтров не определит, что эта функция должна быть включена. На этом этапе флаг функции считается *включенным*, и все остальные результаты фильтрации пропускаются. Если фильтр не указывает, что эта функция должна быть включена, флаг функции остается *выключенным*.

Диспетчер функций поддерживает файл *appsettings.json* в качестве источника конфигурации для флагов функций. В примере ниже показано, как настроить флаги функций в JSON-файле.

```JSON
"FeatureManagement": {
    "FeatureX": true, // Feature flag set to on
    "FeatureY": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

В соответствии с соглашением раздел `FeatureManagement` этого JSON-документа используется для параметров флагов функций. Приведенный выше пример показывает три флага функций с фильтрами, определенными в свойстве *EnabledFor*:

* **FeatureA** — *on*.
* **FeatureB** — *off*.
* **FeatureC** указывает фильтр с именем *Percentage* и свойством *Parameters*. *Percentage* является примером настраиваемого фильтра и определяет 50-процентную вероятность того, что флаг **FeatureC** будет иметь значение *on*.

## <a name="referencing"></a>Указание ссылки

Флаги функций должны быть определены как переменные `enum` (хотя это необязательно), чтобы на них можно было легко ссылаться в коде.

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-check"></a>Проверка флага функции

Базовый шаблон управления функциями — сначала проверяется, *включен* ли флаг функции, а затем выполняются вложенные действия (при наличии).

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Внедрение зависимостей

В ASP.NET Core MVC доступ к диспетчеру функций `IFeatureManager` можно получить с помощью внедрения зависимостей.

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-action"></a>Действие контроллера

В контроллерах MVC атрибут `Feature` может использоваться для управления объектом включения: весь класс контроллера или определенное действие. Для следующего контроллера `HomeController` необходимо, чтобы *FeatureA* была *включена*, прежде чем любое содержащееся в нем действие будет выполнено.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Для выполнения следующего действия `Index` требуется, чтобы *FeatureA* была *включена*.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Когда контроллер MVC или действие блокируется из-за того, что флаг функции контроллера *отключен*, вызывается зарегистрированный обработчик `IDisabledFeatureHandler`. Обработчик `IDisabledFeatureHandler` по умолчанию возвращает клиенту код состояния 404 без текста ответа.

## <a name="view"></a>Просмотр

В представлениях MVC тег `<feature>` можно использовать для отображения содержимого на основе того, включен ли флаг функции.

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filter"></a>Фильтр MVC

Фильтры MVC можно настроить таким образом, чтобы они активировались в зависимости от состояния флага функции. Следующий код добавляет фильтр MVC с именем `SomeMvcFilter`. Этот фильтр активируется в пределах конвейера MVC только в случае, если функция *FeatureA* включена.

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="route"></a>Маршрутизация

Маршруты могут быть предоставлены динамически в зависимости от флагов функций. Следующий код добавляет маршрут, который задает `Beta` в качестве контроллера по умолчанию, только если функция *FeatureA* включена.

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>ПО промежуточного слоя

Флаги функций можно использовать для добавления ветвей приложений и ПО промежуточного слоя в случае выполнения условия. Следующий код вставляет компонент ПО промежуточного слоя в конвейер запроса только в том случае, если функция *FeatureA* включена.

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Следующий код представляет более общую возможность разветвления всего приложения на основе флага функции.

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как с помощью библиотек `Microsoft.FeatureManagement` реализовать флаги функций в приложении ASP.NET Core. Дополнительные сведения о поддержке управления функциями в службе "Конфигурация приложений" и ASP.NET Core см. по следующим ссылкам.

* [Пример кода для флага функции ASP.NET Core](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Документация по Microsoft.FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Руководство по управлению флагами функций](./manage-feature-flags.md)
