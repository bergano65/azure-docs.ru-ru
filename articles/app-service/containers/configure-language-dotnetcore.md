---
title: Настройка приложения ASP.NET Core — служба приложений Azure | Документация Майкрософт
description: Сведения о настройке приложения ASP.NET Core на работу в службе приложений Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: f2781e3cc2433f73ba7ff33e5c452e29de746adf
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956202"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Настройка виртуальных машин Linux приложения ASP.NET Core для службы приложений Azure

Приложения ASP.NET Core должен быть развернут как скомпилированные двоичные файлы. Средство публикации Visual Studio выполняет сборку решения, а затем развернет скомпилированные двоичные файлы напрямую, тогда как механизма развертывания службы приложений развертывается в первую очередь в репозитории кода и компиляция двоичных файлов.

Это руководство содержит основные понятия и инструкции по ASP.NET Core разработчики, использующие встроенный контейнер Linux в службе приложений. Если вы раньше не использовали службы приложений Azure, выполните [быстрого запуска ASP.NET Core](quickstart-dotnetcore.md) и [ASP.NET Core и руководство по базам данных SQL](tutorial-dotnetcore-sqldb-app.md) первого.

## <a name="show-net-core-version"></a>Показать версию .NET Core

Чтобы отобразить текущую версию .NET Core, выполните следующую команду [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Чтобы отобразить все поддерживаемые версии .NET Core, выполните следующую команду [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Задать версию .NET Core

Выполните следующую команду [Cloud Shell](https://shell.azure.com) задать версию .NET Core до версии 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) вне кода приложения. Затем они будут доступны с помощью стандартного шаблона ASP.NET:

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

Если задан параметр приложения с тем же именем в службе приложений и в *Web.config*, значение службы приложений имеет приоритет над значением Web.config. Значение Web.config можно отлаживать приложение локально, а также значение службы приложений позволяет запуска приложения в продукт с параметрами рабочей среде. Строки подключения работают таким же образом. Таким образом, можно хранить свои секреты приложения вне репозитория кода и доступ к соответствующие значения без изменения кода.

## <a name="get-detailed-exceptions-page"></a>Получите подробные сведения об исключениях страницы

Если ваше приложение ASP.NET создает исключение в отладчике Visual Studio, браузер отображает страницу подробные исключения, но в службе приложений, страница заменяется универсальный **HTTP 500** ошибка или **ошибку во время При обработке вашего запроса.** . Чтобы открыть страницу подробные исключения в службе приложений, добавьте `ASPNETCORE_ENVIRONMENT` параметр приложения в приложение, выполнив следующую команду в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Обнаружение сеанса HTTPS

В Службе приложений [завершение SSL-запросов](https://wikipedia.org/wiki/TLS_termination_proxy) происходит в подсистеме балансировки нагрузки сети, поэтому все HTTPS-запросы достигают вашего приложения в виде незашифрованных HTTP-запросов. Если ваша логика приложения должен знать, если пользователь запрашивает шифруются или нет, настроить пересылки заголовки по промежуточного слоя в *Startup.cs*:

- Настройка по промежуточного слоя с [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) для пересылки `X-Forwarded-For` и `X-Forwarded-Proto` заголовки в `Startup.ConfigureServices`.
- Добавьте диапазоны частных IP-адресов известные сети, таким образом, по промежуточного слоя можно доверять подсистемы балансировки нагрузки службы приложений.
- Вызвать [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) метод в `Startup.Configure` перед вызовом других по промежуточного слоя.

Объединения всех трех элементов, код выглядит как в следующем примере:

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

Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Развертывание решений для нескольких проектов

При развертывании в подсистеме развертывания с репозиторием ASP.NET *.csproj* файл в корневом каталоге, ядро развертывает проект. При развертывании репозиторием ASP.NET с *.sln* файл в корневом каталоге, то ядро выбирает первый веб-сайт или проект веб-приложения, он находит как приложение службы приложений. Это возможно для модуля не для того выбрать проект, который вы хотите.

Чтобы развернуть решение с несколькими проектами, можно указать проект для использования в службе приложений двумя разными способами:

### <a name="using-deployment-file"></a>С помощью файла .deployment

Добавить *.deployment* файл в корневой папке репозитория и добавьте следующий код:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Используя параметры приложения

В <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, добавьте параметр приложения для приложения службы приложений, выполнив следующую команду интерфейса командной строки. Замените  *\<имя_приложения >*,  *\<resource-group-name >*, и  *\<имя проекта >* с соответствующими значениями .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Открытие сеанса SSH в браузере

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Учебник. по приложению ASP.NET Core с Базой данных SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](app-service-linux-faq.md)