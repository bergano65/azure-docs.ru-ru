---
title: Настройка приложений ASP.NET Core Windows
description: Узнайте, как настроить ASP.NET Core приложение в собственных экземплярах Windows службы приложений. В этой статье показаны наиболее распространенные задачи настройки.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 5819fc5b2d6e64d1812dacd88a2a4f840f6e03c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907997"
---
# <a name="configure-a-windows-aspnet-core-app-for-azure-app-service"></a>Настройка приложения ASP.NET Core Windows для службы приложений Azure

> [!NOTE]
> Сведения о ASP.NET в .NET Framework см. в статье [Настройка приложения ASP.NET для службы приложений Azure](configure-language-dotnet-framework.md) .

ASP.NET Core приложения должны быть развернуты в службе приложений Azure как скомпилированные двоичные файлы. Средство публикации Visual Studio создает решение, а затем развертывает скомпилированные двоичные файлы напрямую, в то время как модуль развертывания службы приложений сначала развертывает репозиторий кода, а затем компилирует двоичные файлы. Сведения о приложениях Linux см. [в статье Настройка приложения ASP.NET Core Linux для службы приложений Azure](containers/configure-language-dotnetcore.md).

Это краткое описание содержит основные понятия и инструкции для ASP.NET Core разработчиков. Если вы никогда не использовали службу приложений Azure, сначала следуйте инструкциям в [кратком](app-service-web-get-started-dotnet.md) руководстве по ASP.NET и [ASP.NET Core с базой данных SQL](app-service-web-tutorial-dotnetcore-sqldb.md) .

## <a name="show-supported-net-core-runtime-versions"></a>Отображение поддерживаемых версий среды выполнения .NET Core

В службе приложений на экземплярах Windows уже установлены все поддерживаемые версии .NET Core. Чтобы отобразить доступные версии среды выполнения и пакета SDK .NET Core, перейдите к `https://<app-name>.scm.azurewebsites.net/DebugConsole` следующей команде в консоли на основе браузера и выполните следующую команду:

```azurecli-interactive
dotnet --info
```

## <a name="set-net-core-version"></a>Задать версию .NET Core

Задайте целевую платформу в файле проекта для проекта ASP.NET Core. Дополнительные сведения см. в статье [Выбор версии .NET Core для использования](https://docs.microsoft.com/dotnet/core/versions/selection) в документации по .NET Core.

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](configure-common.md#configure-app-settings) вне кода приложения. Затем к ним можно обращаться в любом классе, используя стандартный шаблон внедрения зависимостей ASP.NET Core:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Если вы настроили параметр приложения с тем же именем в службе приложений и в *appsettings.js*, например, значение службы приложений имеет приоритет над *appsettings.js* значением. Локальная *appsettings.jsпо* значению позволяет выполнять отладку приложения локально, но значение службы приложений позволяет запускать приложение в продукте с параметрами рабочей среды. Строки подключения работают таким же образом. Таким образом вы сможете защитить секреты приложения за пределами репозитория кода и получить доступ к соответствующим значениям без изменения кода.

> [!NOTE]
> Обратите внимание, что [иерархические данные конфигурации](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) в *appsettings.js* , доступ к которым осуществляется с помощью `:` стандартного разделителя .NET Core. Чтобы переопределить конкретный иерархический параметр конфигурации в службе приложений, задайте имя параметра приложения с тем же форматом с разделителями в ключе. в [Cloud Shell](https://shell.azure.com)можно выполнить следующий пример:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Развертывание решений с несколькими проектами

Если решение Visual Studio включает несколько проектов, процесс публикации Visual Studio уже включает в себя выбор развертываемого проекта. При развертывании в подсистеме развертывания службы приложений, например в Git или с ZIP-развертыванием с включенной автоматизацией сборки, подсистема развертывания службы приложений выбирает первый веб-сайт или проект веб-приложения, который он находит в качестве приложения службы приложений. Вы можете указать, какую службу приложений проекта следует использовать, указав `PROJECT` параметр приложения. Например, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

ASP.NET Core предоставляет [встроенный поставщик ведения журнала для службы приложений](https://docs.microsoft.com/aspnet/core/fundamentals/logging/#azure-app-service). В *Program.CS* проекта добавьте поставщик в приложение с помощью `ConfigureLogging` метода расширения, как показано в следующем примере:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Затем можно настроить и создать журналы с помощью [стандартного шаблона .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Дополнительные сведения об устранении неполадок ASP.NET Core приложений в службе приложений см. в статье [Устранение неполадок ASP.NET Core в службе приложений Azure и службах IIS](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis) .

## <a name="get-detailed-exceptions-page"></a>Страница «Получение подробных сведений об исключениях»

Когда приложение ASP.NET Core создает исключение в отладчике Visual Studio, браузер отображает подробное сообщение об исключении, но в службе приложений эта страница заменяется общей ошибкой **HTTP 500** или **при обработке запроса произошла ошибка.** !". Чтобы отобразить страницу подробного исключения в службе приложений, добавьте `ASPNETCORE_ENVIRONMENT` параметр приложения в приложение, выполнив следующую команду в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Обнаружение сеанса HTTPS

В Службе приложений [завершение SSL-запросов](https://wikipedia.org/wiki/TLS_termination_proxy) происходит в подсистеме балансировки нагрузки сети, поэтому все HTTPS-запросы достигают вашего приложения в виде незашифрованных HTTP-запросов. Если логика приложения должна определить, зашифрованы ли запросы пользователя, настройте по промежуточного слоя перенаправляемых заголовков в *Startup.CS*:

- Настройте ПО промежуточного слоя с помощью [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions), чтобы заголовки `X-Forwarded-For` и `X-Forwarded-Proto` перенаправлялись в `Startup.ConfigureServices`.
- Добавьте в известные сети диапазоны частных IP-адресов, чтобы по промежуточного слоя можно было доверять подсистеме балансировки нагрузки службы приложений.
- Вызовите метод [усефорвардедхеадерс](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) в `Startup.Configure` перед вызовом других по промежуточного слоя.

Поместив все три элемента вместе, код будет выглядеть следующим образом:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. по приложению ASP.NET Core с Базой данных SQL](app-service-web-tutorial-dotnetcore-sqldb.md)
