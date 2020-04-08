---
title: Удалить исследования приложений в визуальной студии - Azure Monitor
description: Как удалить Application Insights SDK для ASP.NET и ASP.NET Core в Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80805109"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Как удалить исследования приложений в визуальной студии

Эта статья покажет вам, как удалить ASP.NET и ASP.NET Основные приложения Исследования SDK в Visual Studio.

Чтобы удалить application Insights, необходимо удалить пакеты и ссылки NuGet из API в приложении. Вы можете удалить пакеты NuGet с помощью консоли управления пакетами или управлять nuGet решение в Visual Studio. В следующих разделах будут показаны два способа удаления пакетов NuGet и то, что было автоматически добавлено в проект. Обязательно подтвердите, что добавленные файлы и области с вашим собственным кодом, в котором вы делали звонки в API, удаляются.

## <a name="uninstall-using-the-package-management-console"></a>Удаление с помощью консоли управления пакетом

# <a name="net"></a>[.NET](#tab/net)

1. Чтобы открыть консоль управления пакетами, в верхнем меню выберите Инструменты > менеджер пакета NuGet > консоль менеджера пакетов.
     
    ![В верхнем меню нажмите Инструменты > NuGet менеджер пакетов > консоль менеджер пакетов](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Если сбор следов включен, необходимо сначала удалить Microsoft.ApplicationInsights.TraceListener. Введите `Uninstall-package Microsoft.ApplicationInsights.TraceListener` затем следуйте шагу ниже, чтобы удалить Microsoft.ApplicationInsights.Web.

1. Введите следующую команду: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    После ввода команды пакет Application Insights и все его зависимости будут неустановлены из проекта.
    
    ![Введите команду в консоли](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Чтобы открыть консоль управления пакетами, в верхнем меню выберите Инструменты > менеджер пакета NuGet > консоль менеджера пакетов.

    ![В верхнем меню нажмите Инструменты > NuGet менеджер пакетов > консоль менеджер пакетов](./media/remove-application-insights/package-manager.png)

1. Введите следующую команду: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    После ввода команды пакет Application Insights и все его зависимости будут неустановлены из проекта.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Удалить с помощью visual Studio NuGet UI

# <a name="net"></a>[.NET](#tab/net)

1. В *Solution Explorer* справа, нажмите на **решение** и выберите **Пакеты Управления NuGet для решения.**

    Затем вы увидите экран, который позволяет отсеивать все пакеты NuGet, которые являются частью проекта.
    
     ![Решение правого щелчка в Explorer решения затем выберите пакеты Управления NuGet для решения](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Если сбор следов включен, вам нужно сначала удалить Microsoft.ApplicationInsights.TraceListener без удаления выбранных зависимостей, а затем следовать ниже, чтобы удалить Microsoft.ApplicationInsights.Web с удалением выбранных зависимостей.
    
1. Нажмите на пакет "Microsoft.ApplicationInsights.Web".Справа проверьте флажок рядом с *Проектом,* чтобы выбрать все проекты.
    
1. Чтобы удалить все зависимости при установке, выберите кнопку выпадения **опций** ниже раздела, где вы выбрали проект.

    При *опциях Uninstall*выберите флажок рядом с *зависимостями Удалить.*

1. Выберите **Удалить**.
    
    ![Проверьте удаление зависимостей, а затем удалить](./media/remove-application-insights/uninstall-framework.png)

    Отображается диалоговая коробка, отображая все зависимости, которые должны быть удалены из приложения.Выберите **ОК** для удаления.
    
    ![Проверьте удаление зависимостей, а затем удалить](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  После того, как все не установлено, вы все еще можете увидеть "ApplicationInsights.config" и "AiHandleErrorAttribute.cs" в *Solution Explorer*.Вы можете удалить два файла вручную.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. В *Solution Explorer* справа, нажмите на **решение** и выберите **Пакеты Управления NuGet для решения.**

    Затем вы увидите экран, который позволяет отсеивать все пакеты NuGet, которые являются частью проекта.

    ![Решение правого щелчка в Explorer решения затем выберите пакеты Управления NuGet для решения](./media/remove-application-insights/manage-nuget-core.png)

1. Нажмите на пакет "Microsoft.ApplicationInsights.AspNetCore". Справа проверьте флажок рядом с *Project,* чтобы выбрать все проекты, а затем выберите **Uninstall.**

    ![Проверьте удаление зависимостей, а затем удалить](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Что создается при добавлении приложений

При добавлении приложений В проект создается файлы и код к некоторым файлам. Только установка пакетов NuGet не всегда отбрасывает файлы и код. Чтобы полностью удалить Application Insights, следует проверить и вручную удалить добавленный код или файлы вместе с любыми вызовами API, которые вы добавили в проект.

# <a name="net"></a>[.NET](#tab/net)

При добавлении телеметрии Application Insights в проект Visual Studio ASP.NET, он добавляет следующие файлы:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

Добавлены следующие части кода:

- (Название вашего проекта).csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout.cshtml

    Если в проекте есть файл Layout.cshtml, код ниже добавляется.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.json

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

При добавлении телеметрии Application Insights в проект шаблона Visual Studio ASP.NET Core он добавляет следующий код:

- (Название вашего проекта).csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.json:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Дальнейшие действия

- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)