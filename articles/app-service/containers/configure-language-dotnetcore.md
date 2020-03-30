---
title: Настройка приложений Linux ASP.NET Core
description: Узнайте, как настроить предварительно построенный ASP.NET контейнер Core для вашего приложения. В этой статье показаны наиболее распространенные задачи настройки.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255908"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Настройте приложение Linux ASP.NET Core для службы приложений Azure

ASP.NET основные приложения должны быть развернуты в виде компилированных бинарных файлов. Инструмент публикации Visual Studio создает решение, а затем развертывает компилированные файлы напрямую, в то время как движок развертывания Службы App развертывает сначала репозиторий кода, а затем компилирует файлы.

В этом руководстве содержатся ключевые концепции и инструкции для разработчиков ASP.NET Core, которые используют встроенный контейнер Linux в App Service. Если вы никогда не пользовались службой приложений Azure, следуйте [ASP.NET Core,](quickstart-dotnetcore.md) а [ASP.NET Core с помощью учебника по базам данных S'L.](tutorial-dotnetcore-sqldb-app.md)

## <a name="show-net-core-version"></a>Показать ядра версии .NET

Чтобы показать текущую версию .NET Core, запустите следующую команду в [облачной оболочке:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Чтобы показать все поддерживаемые версии .NET Core, запустите следующую команду в [облачной оболочке:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Набор ядовитого варианта .NET

Запустите следующую команду в [облачной оболочке,](https://shell.azure.com) чтобы настроить версию .NET Core до 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>Настройка автоматизации сборки

Если развертывать приложение с помощью пакетов Git или zip с включенной автоматизацией сборки, служба App Service выполняет этапы автоматизации через следующую последовательность:

1. Выполнить пользовательский `PRE_BUILD_SCRIPT_PATH`скрипт, если указано .
1. Запуск `dotnet restore` для восстановления зависимостей NuGet.
1. Выполнить, `dotnet publish` чтобы построить двоичный для производства.
1. Выполнить пользовательский `POST_BUILD_SCRIPT_PATH`скрипт, если указано .

`PRE_BUILD_COMMAND`и `POST_BUILD_COMMAND` являются переменными среды, которые по умолчанию пусты. Чтобы запустить команды предварительной `PRE_BUILD_COMMAND`сборки, определите. Чтобы запустить команды после сборки, определите. `POST_BUILD_COMMAND`

В следующем примере указаны две переменные к серии команд, разделенных запятыми.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Для дополнительных переменных среды для [Oryx configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)настройки автоматизации сборки см.

Для получения дополнительной информации о том, как Служба приложений работает и создает ASP.NET основных приложений в Linux, [см.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md)

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) вне кода приложения. Затем вы можете получить к ним доступ в любом классе, используя стандартный ASP.NET шаблон инъекций основных зависимостей:

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

Например, если настроить настройку приложения с тем же именем в Службе app и в *appsettings.json,* значение Службы app имеет приоритет над значением *appsettings.json.* Значение *локального appsettings.json* позволяет отладить приложение локально, но значение Службы app позволяет вам запускать приложение в продукте с настройками производства. Строки соединения работают таким же образом. Таким образом, вы можете хранить секреты приложения за пределами репозитория кода и получать доступ к соответствующим значениям без изменения кода.

## <a name="get-detailed-exceptions-page"></a>Получить подробную страницу исключений

Когда приложение ASP.NET генерирует исключение в отладчике Visual Studio, браузер отображает подробную страницу исключения, но в Службе приложения эта страница заменяется общей ошибкой **HTTP 500** или **ошибкой, возникшой при обработке запроса.** . Чтобы отобразить подробную страницу `ASPNETCORE_ENVIRONMENT` исключения в Службе приложения, добавьте настройку приложения в приложение, запустив следующую команду в <a target="_blank" href="https://shell.azure.com" >облачной оболочке.</a>

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Обнаружение сеанса HTTPS

В Службе приложений [завершение SSL-запросов](https://wikipedia.org/wiki/TLS_termination_proxy) происходит в подсистеме балансировки нагрузки сети, поэтому все HTTPS-запросы достигают вашего приложения в виде незашифрованных HTTP-запросов. Если логике приложения необходимо знать, зашифрованы запросы пользователей или нет, наймите программу Forwarded Headers Middleware в *Startup.cs:*

- Настройте ПО промежуточного слоя с помощью [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions), чтобы заголовки `X-Forwarded-For` и `X-Forwarded-Proto` перенаправлялись в `Startup.ConfigureServices`.
- Добавьте частные диапазоны IP-адресов в известные сети, чтобы посредник мог доверять балансеру нагрузки Службы App Service.
- Вызовите метод [UseForwardedHeaders,](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) `Startup.Configure` прежде чем вызывать другие промежуточники.

Объединяя все три элемента, код выглядит следующим примером:

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

## <a name="deploy-multi-project-solutions"></a>Развертывание многопроектных решений

При развертывании ASP.NET репозитория в движок развертывания с файлом *.csproj* в корневом каталоге, движок развертывает проект. При развертывании ASP.NET репозитория с файлом *.sln* в корневом каталоге движок выбирает первый веб-сайт или проект web Application, который он находит в качестве приложения Службы Приложения. Двигатель может не выбрать нужный проект.

Для развертывания многопроектного решения можно указать проект для использования в Службе приложений двумя различными способами:

### <a name="using-deployment-file"></a>Использование файла развертывания .

Добавьте файл *развертывания .в* корень репозитория и добавьте следующий код:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Использование настроек приложения

В <a target="_blank" href="https://shell.azure.com">облачной оболочке Azure</a>добавьте настройку приложения в приложение App Service, запустив следующую команду CLI. Замените * \<>, * * \<>группы ресурсов *и * \<>с* именами проектов соответствующими значениями.

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
> [Учебник: ASP.NET основное приложение с базой данных S'L](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](app-service-linux-faq.md)
