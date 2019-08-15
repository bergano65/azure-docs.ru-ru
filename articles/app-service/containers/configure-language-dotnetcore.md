---
title: Настройка ASP.NET Core приложений в службе приложений Azure | Документация Майкрософт
description: Узнайте, как настроить приложения ASP.NET Core для работы в службе приложений Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.openlocfilehash: b05120148d3b82829c465effbcdc948da950aaf0
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990259"
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

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) вне кода приложения. Затем к ним можно обращаться в любом классе, используя стандартный шаблон внедрения зависимостей ASP.NET Core:

```csharp
include Microsoft.Extensions.Configuration;

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

Когда приложение ASP.NET создает исключение в отладчике Visual Studio, браузер отображает подробное сообщение об исключении, но в службе приложений эта страница заменяется общей ошибкой **HTTP 500** или **при обработке запроса произошла ошибка.** . Чтобы отобразить страницу подробного исключения в службе приложений, добавьте `ASPNETCORE_ENVIRONMENT` параметр приложения в приложение, выполнив следующую команду в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Обнаружение сеанса HTTPS

В Службе приложений [завершение SSL-запросов](https://wikipedia.org/wiki/TLS_termination_proxy) происходит в подсистеме балансировки нагрузки сети, поэтому все HTTPS-запросы достигают вашего приложения в виде незашифрованных HTTP-запросов. Если логика приложения должна определить, зашифрованы ли запросы пользователя, настройте по промежуточного слоя перенаправляемых заголовков в *Startup.CS*:

- Настройте по промежуточного слоя с [форвардедхеадерсоптионс](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) , `X-Forwarded-For` чтобы `X-Forwarded-Proto` переслать `Startup.ConfigureServices`заголовки и в.
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

Дополнительные сведения см. [в статье настройка ASP.NET Core для работы с прокси-серверами и](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)подсистемами балансировки нагрузки.

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

В <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>добавьте параметр приложения в приложение службы приложений, выполнив следующую команду CLI. *Замените\<имя приложения >* ,  *\<Resource-Group-name >* и  *\<именем проекта >* соответствующими значениями.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Открытие сеанса SSH в браузере

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Учебник. по приложению ASP.NET Core с Базой данных SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](app-service-linux-faq.md)