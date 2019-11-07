---
title: Краткое руководство по использованию Службы Azure SignalR
description: Краткое руководство по использованию службы Azure SignalR для создания комнаты чата с помощью приложений MVC для ASP.NET Core.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: 022780f2b37c8bed49c81774d443b69bae41e5e7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476752"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Краткое руководство. Создание чата с помощью Службы SignalR


Azure SignalR — это служба Azure, которая помогает разработчикам без труда создавать веб-приложения с функциями в режиме реального времени. Эта служба основана на [SignalR для ASP.NET Core 2.1](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1), но также поддерживает [SignalR для ASP.NET Core 3.0](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0).

В этой статье показано, как приступить к работе со службой Azure SignalR. В этом кратком руководстве вы создадите приложение чата с помощью веб-приложения MVC для ASP.NET Core. Это приложение установит соединение с ресурсом службы Azure SignalR для включения обновлений содержимого в режиме реального времени. Вы разместите веб-приложение локально и подключите к нему несколько браузерных клиентов. Каждый клиент сможет принудительно отправлять обновления содержимого остальным клиентам. 

Вы можете использовать любой редактор кода для выполнения шагов в этом кратком руководстве. Одним из вариантов является [Visual Studio Code](https://code.visualstudio.com/), который доступен на платформах Windows, macOS и Linux.

Код для этого руководства доступен для скачивания в [репозитории примеров AzureSignalR на GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Вы также можете создать ресурсы Azure, используемые в этом кратком руководстве, с помощью [скрипта создания Службы SignalR](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Предварительные требования

* Установите [пакет SDK для .NET Core](https://www.microsoft.com/net/download/windows).
* Скачанный или клонированный репозиторий [примеров AzureSignalR](https://github.com/aspnet/AzureSignalR-samples), доступный в репозитории GitHub. 

## <a name="create-an-azure-signalr-resource"></a>Создание ресурса Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Создание веб-приложения ASP.NET Core

В этом разделе вы примените [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/) для создания проекта веб-приложения MVC для ASP.NET Core. Преимущество использования .NET Core CLI по сравнению с Visual Studio заключается в том, что он доступен на платформах Windows, macOS и Linux. 

1. Создайте каталог для своего проекта. В этом кратком руководстве используется папка *E:\Testing\chattest*.

2. В новой папке выполните следующую команду, чтобы создать проект:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Добавление менеджера секретных ключей в проект

В этом разделе вы добавите в проект инструмент [Диспетчер секретов](https://docs.microsoft.com/aspnet/core/security/app-secrets). Инструмент "Диспетчер секретов" хранит конфиденциальные данные для разработки вне вашего дерева проектов. Такой подход помогает предотвратить случайную компрометацию секретов приложений в исходном коде.

1. Откройте файл с расширением *CSPROJ*. Добавьте элемент `DotNetCliToolReference`, чтобы включить *Microsoft.Extensions.SecretManager.Tools*. Кроме того, добавьте элемент `UserSecretsId`, как показано в следующем коде *chattest.csproj*, и сохраните файл.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Добавление веб-приложения Azure SignalR

1. Добавьте ссылку на пакет NuGet `Microsoft.Azure.SignalR`, выполнив следующую команду:

        dotnet add package Microsoft.Azure.SignalR

2. Выполните следующую команду, чтобы восстановить пакеты проекта:

        dotnet restore

3. Добавьте секрет с именем *Azure: SignalR: ConnectionString* в диспетчер секретов. 

    Этот секрет будет содержать строку подключения для получения доступа к ресурсам службы SignalR. *Azure:SignalR:ConnectionString* — ключ конфигурации по умолчанию, который SignalR ищет для установки подключения. Замените значение, включенное в приведенную ниже команду, строкой подключения к реальному ресурсу службы SignalR.

    Эту команду необходимо выполнять в том же каталоге, где расположен *CSPROJ*-файл.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    Диспетчер секретов будет использоваться только для тестирования веб-приложения при локальном размещении. В следующем руководстве вы развернете веб-приложение чата в Azure. После развертывания веб-приложения в Azure вы будете использовать параметр приложения, а не строку подключения из диспетчера секретов.

    Доступ к этому секрету осуществляется через API конфигурации. Двоеточие (:) используется в имени конфигурации при работе с API конфигурации на всех поддерживаемых платформах. Дополнительные сведения см. в статье [Конфигурация в .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Откройте файл *Startup.cs* и обновите метод `ConfigureServices` для использования службы Azure SignalR, вызвав метод `services.AddSignalR().AddAzureSignalR()` (только для ASP.NET Core 2):

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```
    Для ASP.NET Core 3+ не требуется никаких изменений в методе `ConfigureServices`.

    В этом коде не указан параметр `AddAzureSignalR()`, а значит в нем для строки подключения к ресурсу SignalR используется ключ конфигурации по умолчанию. По умолчанию используется ключ конфигурации *Azure:SignalR:ConnectionString*.

5. Кроме того, в файле *Startup.cs* обновите метод `Configure`, заменив вызов к `app.UseStaticFiles()` следующим кодом, и сохраните файл (только для ASP.NET Core 2).

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    Для ASP.NET Core 3+ замените приведенный выше код на следующий:

    ```csharp
    app.UseFileServer();
    app.UseRouting();
    app.UseAuthorization();

    app.UseEndpoints(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```

### <a name="add-a-hub-class"></a>Добавление класса концентратора

В SignalR основным компонентом является концентратор, который предоставляет набор методов для вызова из клиента. В этом разделе необходимо определить класс концентратора с двумя методами: 

* `Broadcast`: этот метод осуществляет широковещательную рассылку сообщений всем клиентам.
* `Echo`: этот метод отправляет сообщение обратно в вызывающий объект.

Оба метода используют интерфейс `Clients`, предоставляемый пакетом SDK для SignalR и ASP.NET Core. Этот интерфейс предоставляет доступ ко всем подключенным клиентам, позволяя отправлять в них содержимое.

1. В каталоге проекта добавьте новую папку с именем *Hub*. Добавьте новый файл с кодом концентратора с именем *Chat.cs* в новую папку.

2. Добавьте в файл *Chat.cs* следующий код, который определяет класс концентратора, и сохраните файл. 

    Обновите пространство имен для этого класса, если используется имя проекта, отличное от *chattest*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Добавление клиентского интерфейса для веб-приложения

Пользовательский интерфейс клиента для этого приложения чата представлен в коде HTML и JavaScript в файле с именем *index.html* в каталоге *wwwroot*.

Скопируйте файл *index.html*, папки *css* и *scripts* из папки *wwwroot* [хранилища примеров ](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Вставьте их в папку *wwwroot* своего проекта.

Так выглядит основной код файла *index.html*: 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

Код в файле *index.html* вызывает `HubConnectionBuilder.build()` для установки HTTP-подключения к ресурсу Azure SignalR.

Если подключение выполнено успешно, это соединение передается в `bindConnectionMessage`, при этом добавляются обработчики событий для входящей передачи содержимого в клиент. 

`HubConnection.start()` запускает обмен данными с концентратором. Затем `onConnected()` добавляет обработчики событий кнопки. Эти обработчики используют это соединение, чтобы позволить клиенту передавать обновления содержимого для всех подключенных клиентов.

## <a name="add-a-development-runtime-profile"></a>Добавление профиля среды выполнения разработки

В этом разделе вы добавите среду выполнения для разработки на ASP.NET Core. Дополнительные сведения см. в статье [Использование нескольких сред в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Создайте в проекте папку с именем *Properties*.

2. Добавьте в эту папку новый файл с именем *launchSettings.json*, поместите в него следующее содержимое и сохраните файл.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Чтобы создать приложение с помощью .NET Core CLI, выполните следующую команду в оболочке командной строки:

        dotnet build

2. Когда сборка завершится, запустите веб-приложение локально с помощью следующей команды:

        dotnet run

    Приложение будет размещено на порту 5000 локального компьютера, как указано в профиле среды выполнения разработки:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Откройте два окна браузера. В каждом из них откройте адрес `http://localhost:5000`. Вам будет предложено ввести имя. Введите имя клиента для обоих клиентов и протестируйте передачу сообщений между клиентами с помощью кнопки **Отправить**.

    ![Пример групповой беседы Azure SignalR](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Очистка ресурсов

Для работы со следующим руководством можно сохранить и использовать ресурсы, созданные в этом кратком руководстве.

Если вы не намерены далее работать с примером приложения из этого краткого руководства, удалите созданные для него ресурсы Azure, чтобы избежать ненужных расходов. 

> [!IMPORTANT]
> Удаление группы ресурсов необратимо и распространяется на все ресурсы в группе. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если ресурсы для размещения этого примера созданы в имеющейся группе ресурсов, которая содержит другие нужные ресурсы, каждый ресурс можно удалить отдельно через колонку приложения, чтобы не удалять группу ресурсов.
> 
> 

Войдите в портал [Azure](https://portal.azure.com) и выберите**Группы ресурсов**.

Введите имя группы ресурсов в текстовое поле **Фильтровать по имени**. В инструкциях к этому краткому руководству была использована группа ресурсов с именем *SignalRTestResources*. В списке результатов щелкните значок многоточия ( **...** ) рядом с этой группой ресурсов и выберите действие **Удалить группу ресурсов**.

   
![Последовательность действий для удаления группы ресурсов](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Подтвердите операцию удаления группы ресурсов. Введите имя группы ресурсов, которую необходимо удалить, и нажмите **Удалить**.
   
Через некоторое время группа ресурсов и все ее ресурсы будут удалены.



## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали ресурс Службы Azure SignalR. Затем вы использовали его в веб-приложении ASP.NET Core, чтобы в реальном времени передавать обновленное содержимое в несколько подключенных клиентов. Чтобы получить дополнительные сведения об использовании Службы Azure SignalR, перейдите к руководству по проверке подлинности.

> [!div class="nextstepaction"]
> [Tutorial: Azure SignalR Service authentication](./signalr-concept-authenticate-oauth.md) (Руководство по аутентификации службы Azure SignalR)


