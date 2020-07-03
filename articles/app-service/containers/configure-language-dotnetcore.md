---
title: Настройка приложений ASP.NET Core Linux
description: Узнайте, как настроить предварительно созданный контейнер ASP.NET Core для приложения. В этой статье показаны наиболее распространенные задачи настройки.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78255908"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Настройка приложения ASP.NET Core Linux для службы приложений Azure

ASP.NET Core приложения должны быть развернуты в виде скомпилированных двоичных файлов. Средство публикации Visual Studio создает решение, а затем развертывает скомпилированные двоичные файлы напрямую, в то время как модуль развертывания службы приложений сначала развертывает репозиторий кода, а затем компилирует двоичные файлы.

Это краткое описание содержит основные понятия и инструкции для ASP.NET Core разработчиков, использующих встроенный контейнер Linux в службе приложений. Если вы никогда не использовали службу приложений Azure, сначала следуйте инструкциям в руководстве по [ASP.NET Core](quickstart-dotnetcore.md) и [ASP.NET Core с базой данных SQL](tutorial-dotnetcore-sqldb-app.md) .

## <a name="show-net-core-version"></a>Показывать версию .NET Core

Чтобы отобразить текущую версию .NET Core, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Чтобы отобразить все поддерживаемые версии .NET Core, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Задать версию .NET Core

Выполните следующую команду в [Cloud Shell](https://shell.azure.com) , чтобы установить версию .NET Core равным 2,1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>Настройка автоматизации сборки

Если приложение развертывается с использованием Git или zip-пакетов с включенной автоматизацией сборки, то Автоматизация сборки службы приложений проходит через следующую последовательность:

1. Запустить пользовательский скрипт, если он `PRE_BUILD_SCRIPT_PATH`указан в.
1. Выполните `dotnet restore` команду, чтобы восстановить зависимости NuGet.
1. Выполните `dotnet publish` команду, чтобы создать двоичный файл для рабочей среды.
1. Запустить пользовательский скрипт, если он `POST_BUILD_SCRIPT_PATH`указан в.

`PRE_BUILD_COMMAND`и `POST_BUILD_COMMAND` представляют собой переменные среды, которые по умолчанию являются пустыми. Чтобы выполнить команды перед сборкой, определите `PRE_BUILD_COMMAND`. Чтобы выполнить команды после сборки, определите `POST_BUILD_COMMAND`.

В следующем примере указываются две переменные для ряда команд, разделенных запятыми.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Дополнительные переменные среды для настройки автоматизации сборки см. в разделе [Орикс Configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Дополнительные сведения о том, как служба приложений работает и создает ASP.NET Core приложений в Linux, см. в [документации по Орикс: как обнаруживаются и строятся приложения .NET Core](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) вне кода приложения. Затем к ним можно обращаться в любом классе, используя стандартный шаблон внедрения зависимостей ASP.NET Core:

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
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Например, если настроить параметр приложения с тем же именем в службе приложений и в *appSettings. JSON*, то значение службы приложений имеет приоритет над значением *appSettings. JSON* . Локальное значение *appSettings. JSON* позволяет выполнять отладку приложения локально, но значение службы приложений позволяет запускать приложение в продукте с параметрами рабочей среды. Строки подключения работают таким же образом. Таким образом вы сможете защитить секреты приложения за пределами репозитория кода и получить доступ к соответствующим значениям без изменения кода.

## <a name="get-detailed-exceptions-page"></a>Страница «Получение подробных сведений об исключениях»

Когда приложение ASP.NET создает исключение в отладчике Visual Studio, браузер отображает подробное сообщение об исключении, но в службе приложений эта страница заменяется общей ошибкой **HTTP 500** или **при обработке запроса произошла ошибка.** !". Чтобы отобразить страницу подробного исключения в службе приложений, добавьте параметр `ASPNETCORE_ENVIRONMENT` приложения в приложение, выполнив следующую команду в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

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

## <a name="deploy-multi-project-solutions"></a>Развертывание решений с несколькими проектами

При развертывании репозитория ASP.NET в подсистеме развертывания с файлом *CSPROJ* в корневом каталоге обработчик развертывает проект. При развертывании репозитория ASP.NET с расширением *SLN* в корневом каталоге подсистема выбирает первый веб-сайт или проект веб-приложения, который он находит в качестве приложения службы приложений. Подсистема может не выбирать нужный проект.

Чтобы развернуть многопроектное решение, можно указать проект для использования в службе приложений двумя разными способами.

### <a name="using-deployment-file"></a>Использование файла. Deployment

Добавьте файл *. Deployment* в корень репозитория и добавьте следующий код:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Использование параметров приложения

В <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>добавьте параметр приложения в приложение службы приложений, выполнив следующую команду CLI. Замените * \<имя приложения>*, * \<Resource-Group-Name>* и * \<именем проекта>* соответствующими значениями.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Открытие сеанса SSH в браузере

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. по приложению ASP.NET Core с Базой данных SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](app-service-linux-faq.md)
