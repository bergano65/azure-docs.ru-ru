---
title: Руководство по Использование ASP.NET Core с Базой данных SQL
description: Узнайте, как создать приложение .NET Core, работающее в службе приложений Azure, с подключением к базе данных SQL.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/23/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 4b5c78313eddd50441dbd47f556d2dbef03feefc
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310399"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>Руководство по Создание приложения ASP.NET Core и Базы данных SQL в Службе приложений Azure

> [!NOTE]
> В этой статье мы развернем приложение в службе приложений на платформе Windows. Дополнительные сведения о развертывании в Службе приложений в _Linux_ см. в руководстве по [созданию приложения .NET Core с Базой данных SQL в Службе приложений Azure в Linux](./containers/tutorial-dotnetcore-sqldb-app.md).
>

[Служба приложений](overview.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости в Azure. В этом руководстве показано, как создать приложение .NET Core и подключить его к базе данных SQL. После выполнения всех действий у вас будет приложение .NET Core MVC, работающее в службе приложений.

![Приложение, работающее в службе приложений](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

В этом руководстве описано следующее:

> [!div class="checklist"]
>
> * Создание базы данных в службе "База данных SQL Azure"
> * Подключение приложения .NET Core к базе данных SQL.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством сделайте следующее:

* <a href="https://git-scm.com/" target="_blank">установите Git</a>;
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">установите пакет SDK для .NET Core 3.1 с последними обновлениями</a>.

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
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

Откройте браузер и перейдите по адресу `http://localhost:5000`. Щелкните ссылку **Создать**, чтобы создать несколько элементов _списка дел_.

![Успешное подключение к базе данных SQL](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

Чтобы остановить приложение .NET Core в любое время, нажмите `Ctrl+C` в окне терминала.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-database-in-azure-sql-database"></a>Создание базы данных в службе "База данных SQL Azure"

На этом шаге вы создадите базу данных в службе [База данных SQL Azure](/azure/sql-database/). При развертывании приложения в Azure используется эта база данных.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-server-in-azure-sql-database"></a>Создание сервера в службе "База данных SQL Azure"

В Cloud Shell создайте [сервер](../azure-sql/database/logical-servers.md) в службе "База данных SQL Azure", выполнив команду [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create). Сервер SQL — это логическая конструкция, которая содержит группу баз данных, которыми можно управлять как группой.

Замените заполнитель *\<server-name>* *уникальным* именем. Это имя используется как часть глобально уникальной конечной точки Базы данных SQL: `<server-name>.database.windows.net`. Допустимые символы: `a`-`z`, `0`-`9`, `-`. Кроме того, замените *\<db-username>* и *\<db-password>* именем пользователя и паролем по своему усмотрению.

```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

После создания сервера в Azure CLI отображаются следующие сведения:

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Настройка правил брандмауэра сервера

Создайте [правило брандмауэра на уровне сервера](../azure-sql/database/firewall-configure.md) с помощью команды [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Если для начального и конечного IP-адресов задано значение 0.0.0.0, брандмауэр открыт только для других ресурсов Azure.

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Вы можете применить еще более строгие ограничения в правиле брандмауэра, [разрешив только исходящие IP-адреса, используемые приложением](overview-inbound-outbound-ips.md#find-outbound-ips).

В Cloud Shell повторно выполните соответствующую команду, чтобы разрешить доступ с локального компьютера, заменив *\<your-ip-address>* [локальным IPv4-адресом](https://www.whatsmyip.org/).

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <server_name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database-in-azure-sql-database"></a>Создание базы данных в службе "База данных SQL Azure"

Создайте на сервере базу данных с [уровнем производительности S0](../azure-sql/database/service-tiers-dtu.md) с помощью команды [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Создание строки подключения

Получите строку подключения с помощью команды [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string).

```azurecli-interactive
az sql db show-connection-string --client ado.net --server <server-name> --name coreDB
```

В выходных данных команды замените *\<username>* и *\<password>* на учетные данные администратора базы данных, которые вы использовали ранее.

Это строка подключения для вашего приложения .NET Core. Скопируйте ее для дальнейшего использования.

### <a name="configure-app-to-connect-to-the-database-in-azure"></a>Настройка приложения для подключения к базе данных в Azure

В локальном репозитории откройте файл Startup.cs и найдите такой код:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Замените его следующим кодом.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> Для рабочих приложений, масштаб которых нужно увеличить горизонтально, следуйте рекомендациям из раздела [Применение миграций в рабочей среде](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).

### <a name="run-database-migrations-to-the-database-in-azure"></a>Выполнение миграций баз данных в базу данных в Azure

В настоящее время приложение подключается к локальной базе данных Sqlite. Теперь, когда вы настроили Базу данных SQL Azure, повторно создайте первоначальную миграцию для нее.

В корне репозитория выполните следующие команды. Замените *\<connection-string>* строкой подключения, скопированной ранее.

```
# Delete old migrations
rm Migrations -r
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>Запуск приложения с новой конфигурацией

Теперь, когда миграция базы данных выполняется в рабочей базе данных, протестируйте приложение, выполнив команду:

```
dotnet run
```

Откройте браузер и перейдите по адресу `http://localhost:5000`. Щелкните ссылку **Создать**, чтобы создать несколько элементов _списка дел_. Теперь приложение считывает данные из рабочей базы данных и записывает их в нее.

Зафиксируйте локальные изменения, а затем зафиксируйте их в репозитории Git.

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

Теперь все готово для развертывания кода.

## <a name="deploy-app-to-azure"></a>Развертывание приложения в Azure

На этом шаге вы развернете приложение .NET Core, подключенное к базе данных SQL, в службе приложений.

### <a name="configure-local-git-deployment"></a>Настройка локального развертывания Git

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Создание веб-приложения

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)]

### <a name="configure-connection-string"></a>Настройка строки подключения

Чтобы задать строки подключения для приложения Azure, используйте команду [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в Cloud Shell. В следующей команде замените *\<app-name>* . Также замените параметр *\<connection-string>* строкой подключения, созданной ранее.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

В ASP.NET Core можно использовать эту именованную строку подключения (`MyDbConnection`) со стандартным шаблоном, как и любую строку подключения, указанную в файле *appsettings.json*. В этом случае `MyDbConnection` также определяется в файле *appsettings.json*. Если вы работаете в Службе приложений, определенная в ней строка подключения имеет приоритет над строкой подключения, определенной в файле *appsettings.json*. Код использует значение, указанное в файле *appsettings.json*, во время локальной разработки, и тот же код использует значение Службы приложений при развертывании.

Сведения о том, как в коде указывается ссылка на строку подключения, см. в разделе [Подключение к базе данных SQL в рабочей среде](#configure-app-to-connect-to-the-database-in-azure).

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Переход к приложению Azure

Перейдите к развернутому приложению в веб-браузере.

```bash
http://<app-name>.azurewebsites.net
```

Добавьте несколько элементов списка дел.

![Приложение, работающее в службе приложений](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**Поздравляем!** Вы запустили приложение .NET Core на основе данных в службе приложений.

## <a name="update-locally-and-redeploy"></a>Обновление на локальном компьютере и повторное развертывание

На этом шаге вы измените схему базы данных и опубликуете ее в Azure.

### <a name="update-your-data-model"></a>Обновление модели данных

Откройте файл _Models\Todo.cs_ в редакторе кода. Добавьте в класс `ToDo` следующее свойство:

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>Повторный запуск миграций баз данных

Выполните несколько команд, чтобы внести обновления в рабочую базу данных.

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

### <a name="use-the-new-property"></a>Использование нового свойства

Внесите некоторые изменения в код, чтобы использовалось свойство `Done`. Для простоты мы изменим только представления `Index` и `Create`, чтобы просмотреть свойство в действии.

Откройте файл _Controllers\TodosController.cs_.

Найдите метод `Create([Bind("ID,Description,CreatedDate")] Todo todo)` и добавьте `Done` в список свойств атрибута `Bind`. Когда все будет готово, сигнатура метода `Create()` должна выглядеть следующим образом:

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

Когда команда `git push` будет выполнена, перейдите к приложению Службы приложений, попробуйте добавить элемент задачи и установите флажок **Готово**.

![Приложение Azure после включения Code First Migration](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

Все имеющиеся элементы списка дел по-прежнему отображаются. При повторной публикации приложения ASP.NET Core существующие данные в Базе данных SQL не теряются. Кроме того, Entity Framework Core Migrations изменяет только схему данных, оставляя существующие данные нетронутыми.

## <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики

При запуске приложения ASP.NET Core в Службе приложений Azure можно передавать журналы консоли в Cloud Shell. Таким образом, вы будете получать те же диагностические сообщения, которые помогут устранить ошибки приложения.

В примере проекта уже выполнены инструкции из руководства [Ведение журналов в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) и внесены два изменения в настройки:

* в файле *DotNetCoreSqlDb.csproj* содержится ссылка на `Microsoft.Extensions.Logging.AzureAppServices`.
* Вызовы `loggerFactory.AddAzureWebAppDiagnostics()` в *Program.cs*.

Чтобы в Службе приложений для [уровня ведения журнала](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) ASP.NET Core задать значение `Information` вместо значения по умолчанию `Error`, используйте команду [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) в Cloud Shell.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> Для уровня ведения журнала проекта уже задано значение `Information` в файле *appsettings.json*.

Чтобы настроить потоки для журналов, выполните команду [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) в Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

После настройки потоков обновите приложение Azure в браузере, чтобы получить немного трафика. Вы должны увидеть, что журналы консоли теперь направляются в терминал. Если журналы консоли не отображаются, проверьте еще раз через 30 секунд.

Чтобы остановить потоковую передачу журналов, нажмите клавиши `Ctrl`+`C`.

Дополнительные сведения о настройке журналов ASP.NET Core см. в статье [Ведение журналов в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Управление приложением Azure

Чтобы просмотреть созданное приложение, на [портале Azure](https://portal.azure.com) найдите в поиске и выберите **Службы приложений**.

![Выберите службы приложений на портале Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

На странице **Службы приложений** выберите имя приложения Azure.

![Переход к приложению Azure на портале](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

По умолчанию на портале отображается страница **Обзор** приложения. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. На вкладках в левой части страницы отображаются различные страницы конфигурации, которые можно открыть.

![Страница службы приложений на портале Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Дальнейшие действия

Вы научились выполнять следующие задачи:

> [!div class="checklist"]
>
> * Создание базы данных в службе "База данных SQL Azure"
> * Подключение приложения .NET Core к базе данных SQL.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов из Azure в окно терминала.
> * Управление приложением на портале Azure.

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с приложением.

> [!div class="nextstepaction"]
> [Руководство. Сопоставление настраиваемого DNS-имени с приложением](app-service-web-tutorial-custom-domain.md)
