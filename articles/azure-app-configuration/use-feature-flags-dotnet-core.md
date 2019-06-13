---
title: Руководство по использованию флагов функций в приложении .NET Core | Документация Майкрософт
description: Из этого руководства вы узнаете, как реализовать флаги функций в приложениях .NET Core.
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
ms.openlocfilehash: fc5215f71af45d3273da437fc796bf0d396ba3f9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393519"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>Руководство по Использование флагов компонентов в приложении .NET Core

Библиотеки управления функциями .NET Core обеспечивают идиоматическую поддержку реализации флагов функций в приложении .NET или ASP.NET Core. Эти библиотеки позволяют декларативно добавлять флаги функций в код, чтобы вам не пришлось записывать все операторы `if` для них вручную.

Библиотеки управления функциями также управляют жизненным циклом флагов функций в фоновом режиме. Например, библиотеки отвечают за обновление и кэширование состояний флагов или гарантируют неизменность состояния флага во время вызова запроса. Кроме того, библиотека ASP.NET Core предоставляет готовое решение по интеграции, включая действия контроллера MVC, представления, маршруты и ПО промежуточного слоя.

В [кратком руководстве по добавлению флагов функций в приложение ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) описано несколько использующихся при этом методов. В нашем руководстве такие методы рассматриваются более подробно. Полную справочную документацию см. в [документации по управлению функциями ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410).

Из этого учебника вы узнаете следующее:

> [!div class="checklist"]
> * Добавить флаги функций в ключевых частях приложения для управления доступностью функций.
> * Как выполнить интеграцию с Конфигурацией приложений при использовании этой службы для управления флагами функций.

## <a name="set-up-feature-management"></a>Настройка управления функциями

Диспетчер функций .NET Core `IFeatureManager` возвращает флаги функций из собственной системы конфигурации платформы. Таким образом, вы можете определить флаги функций приложения, используя любой источник конфигурации, поддерживаемый .NET Core, включая локальный файл *appsettings.json* или переменные среды. В `IFeatureManager` используется внедрение зависимостей .NET Core. Вы можете зарегистрировать службы управления функциями с помощью стандартных соглашений:

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

По умолчанию диспетчер функций извлекает флаги функций из раздела `"FeatureManagement"` данных конфигурации .NET Core. В следующем примере диспетчер функций считывает данные из другого раздела под названием `"MyFeatureFlags"`:

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

При использовании фильтров в флагах функций вам необходимо включить дополнительную библиотеку и зарегистрировать ее. В следующем примере показано, как использовать встроенный фильтр функций под названием `PercentageFilter`:

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

Рекомендуем хранить флаги функций вне приложения и управлять ими отдельно. Так вы можете изменять состояния флагов в любое время и немедленно применять эти изменения в приложении. Конфигурация приложений — это централизованное расположение для организации всех флагов функций и управления такими флагами с помощью выделенного пользовательского интерфейса портала. Конфигурация приложений также предоставляет приложению флаги непосредственно через клиентские библиотеки .NET Core.

Самый простой способ подключить приложение ASP.NET Core к Конфигурации приложений — использовать поставщика конфигураций `Microsoft.Extensions.Configuration.AzureAppConfiguration`. Чтобы работать с этим пакетом NuGet, добавьте следующий код в файл *Program.cs*:

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

Значения флагов функций должны изменяться со временем. По умолчанию диспетчер функций обновляет значения флагов функций каждые 30 секунд. В приведенном ниже коде показано, как изменить интервал опроса на 5 секунд в вызове `options.UseFeatureFlags()`:

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Объявление флага функции

Каждый флаг функции состоит из двух частей: имени и списка из одного или нескольких фильтров, использующихся, чтобы оценить, *включена* ли функция (то есть, если его значение равно `True`). Фильтр определяет вариант использования, при котором должна быть включена функция.

Если флаг функции имеет несколько фильтров, список фильтров проходится по порядку, пока один из фильтров не определит, что функция должна быть включена. На этом этапе флаг функции *включен*, и все остальные результаты фильтрации пропускаются. Если фильтр не указывает, что эта функция должна быть включена, флаг функции остается *отключенным*.

Диспетчер функций поддерживает файл *appsettings.json* в качестве источника конфигурации для флагов функций. В следующем примере показано, как настроить флаги функций в JSON-файле:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
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

В соответствии с соглашением раздел `FeatureManagement` этого JSON-документа используется для параметров флагов функций. Предыдущий пример показывает три флага функций с фильтрами, определенными в свойстве `EnabledFor`:

* Флаг `FeatureA` *включен*.
* Флаг `FeatureB` *отключен*.
* `FeatureC` указывает фильтр с именем `Percentage` и свойством `Parameters`. `Percentage` — это настраиваемый фильтр. В этом примере `Percentage` задает 50-процентную вероятность того, что флаг `FeatureC` может быть *включен*.

## <a name="feature-flag-references"></a>Ссылки на флаги функций

Чтобы можно было без труда ссылаться на флаги функций в коде, нужно определить их в виде переменных `enum`:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Проверка флага функции

В базовой схеме управления функциями сначала проверяется, *включен* ли флаг функции. Если это так, диспетчер функций выполняет действия, содержащиеся в функции. Например:

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Внедрение зависимостей

В MVC ASP.NET Core можно получить доступ к диспетчеру функций `IFeatureManager` с помощью внедрения зависимостей:

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

## <a name="controller-actions"></a>Действия контроллера

В контроллерах MVC с помощью атрибута `Feature` можно управлять объектом включения: весь класс контроллера или определенное действие. Для указанного ниже контроллера `HomeController` необходимо, чтобы флаг `FeatureA` был *включен*, прежде чем любое содержащееся в классе контроллера действие будет выполнено.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Для выполнения указанного ниже действия `Index` требуется, чтобы флаг `FeatureA` был *включен*.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Когда контроллер MVC или действие блокируется из-за того, что флаг функции контроллера *отключен*, вызывается зарегистрированный интерфейс `IDisabledFeaturesHandler`. Интерфейс `IDisabledFeaturesHandler` по умолчанию возвращает клиенту код состояния 404 без текста ответа.

## <a name="mvc-views"></a>Представления MVC

В представлениях MVC с помощью тега `<feature>` можно настроить отображение содержимого на основе того, включен ли флаг функции:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Фильтры MVC

Фильтры MVC можно настроить таким образом, чтобы они активировались в зависимости от состояния флага функции. С помощью приведенного ниже кода добавляется фильтр MVC с именем `SomeMvcFilter`. Этот фильтр активируется в пределах конвейера MVC, только если флаг `FeatureA` включен.

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

## <a name="routes"></a>Маршруты

Флаги функций позволяют динамически предоставлять маршруты. С помощью приведенного ниже кода добавляется маршрут, который задает `Beta` в качестве контроллера по умолчанию, только если флаг `FeatureA` включен.

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>ПО промежуточного слоя

Кроме того, флаги функций позволяют добавлять ветви приложений и ПО промежуточного слоя в случае выполнения условия. С помощью приведенного ниже кода компонент ПО промежуточного слоя вставляется в конвейер запроса, только если флаг `FeatureA` включен.

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Следующий код представляет более общую возможность разветвления всего приложения на основе флага функции:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как с помощью библиотек `Microsoft.FeatureManagement` реализовать флаги функций в приложении ASP.NET Core. Подробные сведения о поддержке управления функциями в службе "Конфигурация приложений" и ASP.NET Core см. по следующим ссылкам:

* [Пример кода для флага функции ASP.NET Core](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Документация по Microsoft.FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Руководство по управлению флагами функций](./manage-feature-flags.md)
