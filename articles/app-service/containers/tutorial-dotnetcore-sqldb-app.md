---
title: Создание веб-приложения .NET Core с базой данных SQL в Linux в Службе приложений Azure | Документация Майкрософт
description: Узнайте, как создать приложение ASP.NET Core, работающее в Службе приложений Azure в Linux, с подключением к Базе данных SQL.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4837867188721b13b3f4cb64245ae85a1e32fe50
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656622"
---
# <a name="build-an-aspnet-core-and-sql-database-app-in-azure-app-service-on-linux"></a>Создание веб-приложения ASP.NET Core с Базой данных SQL в Службе приложений Azure в Linux

> [!NOTE]
> В этой статье мы развернем приложение в службе приложений на платформе Linux. Дополнительные сведения о развертывании в Службе приложений в _Windows_ см. в руководстве по [созданию приложения .NET Core с Базой данных SQL в Службе приложений Azure](../app-service-web-tutorial-dotnetcore-sqldb.md).
>

[Служба приложений на платформе Linux](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. В этом руководстве показано, как создать приложение .NET Core и подключить его к Базе данных SQL. После выполнения всех действий у вас будет приложение .NET Core MVC, работающее в службе приложений в Linux.

![Приложение, работающее в службе приложений в Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание базы данных SQL в Azure.
> * Подключение приложения .NET Core к базе данных SQL.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

* [установите Git](https://git-scm.com/);
* [установите .NET Core](https://www.microsoft.com/net/core/).

## <a name="create-local-net-core-app"></a>Создание локального приложения .NET Core

На этом шаге вы настроите локальный проект .NET Core.

### <a name="clone-the-sample-application"></a>Клонирование примера приложения

Откройте окно терминала и c помощью команды `cd` перейдите в рабочий каталог.

Выполните следующие команды, чтобы клонировать пример репозитория и заменить его на корневой репозиторий.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Этот пример проекта содержит простое приложение CRUD, созданное на основе [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Выполнение приложения

Выполните указанные ниже команды, чтобы установить необходимые пакеты, перенести базы данных и запустить приложение.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Откройте браузер и перейдите по адресу `http://localhost:5000`. Щелкните ссылку **Создать**, чтобы создать несколько элементов _списка дел_.

![Успешное подключение к базе данных SQL](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Чтобы остановить приложение .NET Core в любое время, нажмите `Ctrl+C` в окне терминала.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Создание рабочей базы данных SQL

На этом шаге вы создадите базу данных SQL в Azure. При развертывании приложения в Azure используется эта облачная база данных.

В этом руководстве для создания базы данных SQL используется [База данных SQL Azure](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Создание логического сервера базы данных SQL

В Cloud Shell создайте логический сервер базы данных SQL с помощью команды [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create).

Замените заполнитель *\<server-name>* уникальным именем Базы данных SQL. Это имя используется как часть конечной точки базы данных SQL (`<server-name>.database.windows.net`), поэтому оно должно быть уникальным для логических серверов в Azure. В нем могут использоваться только строчные буквы, цифры и дефис (-). Его длина должна быть от 3 до 50 знаков. Кроме того, замените *\<db-username>* и *\<db-password>* именем пользователя и паролем по своему усмотрению. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

После создания логического сервера базы данных SQL в Azure CLI отображаются примерно такие сведения:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server-name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server-name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Настройка правил брандмауэра сервера

Создайте [правило брандмауэра уровня сервера Базы данных SQL Azure](../../sql-database/sql-database-firewall-configure.md) с помощью команды [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Если для начального и конечного IP-адресов задано значение 0.0.0.0, брандмауэр открыт только для других ресурсов Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Создание базы данных

Создайте на сервере базу данных с [уровнем производительности S0](../../sql-database/sql-database-service-tiers-dtu.md) с помощью команды [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Создание строки подключения

В следующей строке замените *\<server-name>* , *\<db-username>* и *\<db-password>* используемыми ранее значениями.

```
Server=tcp:<server-name>.database.windows.net,1433;Database=coreDB;User ID=<db-username>;Password=<db-password>;Encrypt=true;Connection Timeout=30;
```

Это строка подключения для вашего приложения .NET Core. Скопируйте ее для дальнейшего использования.

## <a name="deploy-app-to-azure"></a>Развертывание приложения в Azure

На этом шаге вы развернете приложение .NET Core, подключенное к базе данных SQL, в службе приложений в Linux.

### <a name="configure-local-git-deployment"></a>Настройка локального развертывания Git

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Создание веб-приложения

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Настройка переменной среды

Чтобы задать строки подключения для приложения Azure, используйте команду [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в Cloud Shell. В следующей команде замените *\<app-name>* . Также замените *\<connection-string>* строкой подключения, созданной ранее.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection-string>' --connection-string-type SQLServer
```

Затем задайте для параметра приложения `ASPNETCORE_ENVIRONMENT` значение _Production_. Этот параметр позволяет определить, выполняется ли приложение в Azure, так как SQLite применяется для локальной среды разработки, а база данных SQL — для среды Azure.

В следующем примере настраивается параметр приложения `ASPNETCORE_ENVIRONMENT` в приложении Azure. Замените заполнитель *\<app-name>* .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>Подключение к базе данных SQL в рабочей среде

В локальном репозитории откройте файл Startup.cs и найдите такой код:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Замените его следующим кодом, в котором используются переменные среды, заданные ранее.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Если в коде определено использование рабочей среды (то есть среды Azure), в нем используется строка подключения, настроенная для подключения к базе данных SQL. См. подробнее о [доступе к переменным среды в Службе приложений](configure-language-dotnetcore.md#access-environment-variables).

Вызов `Database.Migrate()` полезен при выполнении в среде Azure, так как он позволяет автоматически создавать базы данных, необходимые для приложения .NET Core, в зависимости от конфигурации миграции.

Сохраните изменения и зафиксируйте их в репозитории Git.

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Переход к приложению Azure

Перейдите к развернутому приложению в веб-браузере.

```bash
http://<app-name>.azurewebsites.net
```

Добавьте несколько элементов списка дел.

![Приложение, работающее в службе приложений в Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Поздравляем!** Вы запустили приложение .NET Core на основе данных в службе приложений в Linux.

## <a name="update-locally-and-redeploy"></a>Обновление на локальном компьютере и повторное развертывание

На этом шаге вы измените схему базы данных и опубликуете ее в Azure.

### <a name="update-your-data-model"></a>Обновление модели данных

Откройте файл _Models\Todo.cs_ в редакторе кода. Добавьте в класс `ToDo` следующее свойство:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Локальный запуск Code First Migrations

Выполните несколько команд, чтобы обновить локальную базу данных.

```bash
dotnet ef migrations add AddProperty
```

Обновите локальную базу данных:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Использование нового свойства

Внесите некоторые изменения в код, чтобы использовалось свойство `Done`. Для простоты мы изменим только представления `Index` и `Create`, чтобы просмотреть свойство в действии.

Откройте файл _Controllers\TodosController.cs_.

Найдите метод `Create()` и добавьте `Done` в список свойств атрибута `Bind`. Когда все будет готово, сигнатура метода `Create()` должна выглядеть следующим образом:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Откройте файл _Views\Todos\Create.cshtml_.

В коде Razor вы должны увидеть элемент `<div class="form-group">` для `Description` и еще один элемент `<div class="form-group">` для `CreatedDate`. Сразу после этих двух элементов добавьте еще один элемент `<div class="form-group">` для `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Откройте файл _Views\Todos\Index.cshtml_.

Найдите пустой элемент `<th></th>`. Добавьте следующий код Razor над этим элементом:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Найдите элемент `<td>`, который содержит вспомогательные функции тегов `asp-action`. Добавьте следующий код Razor над этим элементом:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Это все, что нужно сделать, чтобы увидеть изменения в представлениях `Index` и `Create`.

### <a name="test-your-changes-locally"></a>Проверьте изменения локально.

Запустите приложение на локальном компьютере.

```bash
dotnet run
```

В браузере перейдите по адресу `http://localhost:5000/`. Теперь вы сможете добавить элемент списка дел и установить флажок **Готово**. После этого задание должно появиться на главной странице как выполненное. Помните, что в представлении `Edit` не отображается поле `Done`, так как вы не изменили представление `Edit`.

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

После выполнения команды `git push` перейдите к приложению Azure и проверьте новые функции.

![Приложение Azure после включения Code First Migration](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Все имеющиеся элементы списка дел по-прежнему отображаются. При повторной публикации приложения .NET Core существующие данные в базе данных SQL не теряются. Кроме того, Entity Framework Core Migrations изменяет только схему данных, оставляя существующие данные нетронутыми.

## <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики

В примере проекта уже выполнены инструкции из руководства [Ведение журналов в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) и внесены два изменения в настройки:

- в файле *DotNetCoreSqlDb.csproj* содержится ссылка на `Microsoft.Extensions.Logging.AzureAppServices`.
- в файле *Startup.cs* содержится вызов `loggerFactory.AddAzureWebAppDiagnostics()`.

> [!NOTE]
> Для уровня ведения журнала проекта задано значение `Information` в файле *appsettings.json*.
>

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Дополнительные сведения о настройке журналов ASP.NET Core см. в статье [Ведение журналов в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Управление приложением Azure

Перейдите на [портал Azure](https://portal.azure.com), чтобы увидеть созданное приложение.

В меню слева щелкните **Службы приложений**, а затем — имя своего приложения Azure.

![Переход к приложению Azure на портале](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

По умолчанию на портале отображается страница **Обзор** приложения. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. На вкладках в левой части страницы отображаются различные страницы конфигурации, которые можно открыть.

![Страница службы приложений на портале Azure](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Дополнительная информация

Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создание базы данных SQL в Azure.
> * Подключение приложения .NET Core к базе данных SQL.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов из Azure в окно терминала.
> * Управление приложением на портале Azure.

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с приложением.

> [!div class="nextstepaction"]
> [Руководство. Сопоставление настраиваемого DNS-имени с приложением](../app-service-web-tutorial-custom-domain.md)

Также ознакомьтесь с другими ресурсами:

> [!div class="nextstepaction"]
> [Настройка приложения ASP.NET Core](configure-language-dotnetcore.md)
