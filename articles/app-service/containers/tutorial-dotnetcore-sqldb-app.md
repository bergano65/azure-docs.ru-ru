---
title: Руководство по использованию ASP.NET Core в Linux с базой данных SQL
description: Узнайте, как создать управляемое данными приложение ASP.NET Core в Linux, работающее в Службе приложений Azure, с подключением к Базе данных SQL.
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/23/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 6c2ed68c18cc7845d45bebffc31842879353f2c2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846942"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service-on-linux"></a>Руководство по Создание веб-приложения ASP.NET Core с Базой данных SQL в Службе приложений Azure в Linux

> [!NOTE]
> В этой статье мы развернем приложение в службе приложений на платформе Linux. Дополнительные сведения о развертывании в Службе приложений в _Windows_ см. в руководстве по [созданию приложения .NET Core с Базой данных SQL в Службе приложений Azure](../app-service-web-tutorial-dotnetcore-sqldb.md).
>

[Служба приложений на платформе Linux](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. В этом руководстве показано, как создать приложение .NET Core и подключить его к Базе данных SQL. После выполнения всех действий у вас будет приложение .NET Core MVC, работающее в службе приложений в Linux.

![Приложение, работающее в службе приложений в Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Создание базы данных SQL в Azure.
> * Подключение приложения .NET Core к базе данных SQL.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством сделайте следующее:

* [установите Git](https://git-scm.com/);
* [Установка пакета SDK для .NET Core версии 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2).

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

Замените заполнитель *\<server-name>* *уникальным* именем Базы данных SQL. Это имя используется как часть глобально уникальной конечной точки Базы данных SQL: `<server-name>.database.windows.net`. Допустимые символы: `a`-`z`, `0`-`9`, `-`. Кроме того, замените *\<db-username>* и *\<db-password>* именем пользователя и паролем по своему усмотрению. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

После создания логического сервера базы данных SQL в Azure CLI отображаются примерно такие сведения:

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

Создайте [правило брандмауэра уровня сервера Базы данных SQL Azure](../../sql-database/sql-database-firewall-configure.md) с помощью команды [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Если для начального и конечного IP-адресов задано значение 0.0.0.0, брандмауэр открыт только для других ресурсов Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Вы можете применить еще более строгие ограничения в правиле брандмауэра, [разрешив только исходящие IP-адреса, используемые приложением](../overview-inbound-outbound-ips.md#find-outbound-ips).
>

В Cloud Shell повторно выполните соответствующую команду, чтобы разрешить доступ с локального компьютера, заменив *\<your-ip-address >* [локальным IPv4-адресом](https://www.whatsmyip.org/).

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database"></a>Создание базы данных

Создайте на сервере базу данных с [уровнем производительности S0](../../sql-database/sql-database-service-tiers-dtu.md) с помощью команды [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Создание строки подключения

Получите строку подключения с помощью команды [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string).

```azurecli-interactive
az sql db show-connection-string --client ado.net --server cephalin-core --name coreDB
```

В выходных данных команды замените *\<username>* и *\<password>* на учетные данные администратора базы данных, которые вы использовали ранее.

Это строка подключения для вашего приложения .NET Core. Скопируйте ее для дальнейшего использования.

### <a name="configure-app-to-connect-to-production-database"></a>Настройка приложения для подключения к рабочей базе данных

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
> 

### <a name="run-database-migrations-to-the-production-database"></a>Выполнение миграций баз данных в рабочую базу данных

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
# Bash
export ConnectionStrings__MyDbConnection="<connection-string>"

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

На этом шаге вы развернете приложение .NET Core, подключенное к базе данных SQL, в службе приложений в Linux.

### <a name="configure-local-git-deployment"></a>Настройка локального развертывания Git

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Создание веб-приложения

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)] 

### <a name="configure-connection-string"></a>Настройка строки подключения

Чтобы задать строки подключения для приложения Azure, используйте команду [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в Cloud Shell. В следующей команде замените *\<app-name>* . Также замените *\<connection-string>* строкой подключения, созданной ранее.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

В ASP.NET Core можно использовать эту именованную строку подключения (`MyDbConnection`) со стандартным шаблоном, как и любую строку подключения, указанную в файле *appsettings.json*. В этом случае `MyDbConnection` также определяется в файле *appsettings.json*. Если вы работаете в Службе приложений, определенная в ней строка подключения имеет приоритет над строкой подключения, определенной в файле *appsettings.json*. Код использует значение, указанное в файле *appsettings.json*, во время локальной разработки, и тот же код использует значение Службы приложений при развертывании.

Сведения о том, как в коде указывается ссылка на строку подключения, см. в разделе [Подключение к базе данных SQL в рабочей среде](#configure-app-to-connect-to-production-database).

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

[!INCLUDE [push-to-azure-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 273, done.
Counting objects: 100% (273/273), done.
Delta compression using up to 4 threads
Compressing objects: 100% (175/175), done.
Writing objects: 100% (273/273), 1.19 MiB | 1.85 MiB/s, done.
Total 273 (delta 96), reused 259 (delta 88)
remote: Resolving deltas: 100% (96/96), done.
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cccecf86c5'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote: .
remote: .
remote: .
remote: Done.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/cccecf86c56493ffa594e76ea1deb3abb3702d89/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

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

Откройте файл _Models/Todo.cs_ в редакторе кода. Добавьте в класс `ToDo` следующее свойство:

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>Повторный запуск миграций баз данных

Выполните несколько команд, чтобы внести обновления в рабочую базу данных.

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

> [!NOTE]
> Открывая новое окно терминала, вам придется задать строку подключения к рабочей базе данных в новом терминале, как описано в статье [о выполнении миграций базы данных в рабочей базе данных](#run-database-migrations-to-the-production-database).
>

### <a name="use-the-new-property"></a>Использование нового свойства

Внесите некоторые изменения в код, чтобы использовалось свойство `Done`. Для простоты мы изменим только представления `Index` и `Create`, чтобы просмотреть свойство в действии.

Откройте файл _Controllers/TodosController.cs_.

Найдите метод `Create([Bind("ID,Description,CreatedDate")] Todo todo)` и добавьте `Done` в список свойств атрибута `Bind`. Когда все будет готово, сигнатура метода `Create()` должна выглядеть следующим образом:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Откройте файл _Views/Todos/Create.cshtml_.

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

Откройте файл _Views/Todos/Index.cshtml_.

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

> [!NOTE]
> Открывая новое окно терминала, вам придется задать строку подключения к рабочей базе данных в новом терминале, как описано в статье [о выполнении миграций базы данных в рабочей базе данных](#run-database-migrations-to-the-production-database).
>

В браузере перейдите по адресу `http://localhost:5000/`. Теперь вы сможете добавить элемент списка дел и установить флажок **Готово**. После этого задание должно появиться на главной странице как выполненное. Помните, что в представлении `Edit` не отображается поле `Done`, так как вы не изменили представление `Edit`.

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

Когда команда `git push` будет выполнена, перейдите к приложению Службы приложений, попробуйте добавить элемент задачи и установите флажок **Готово**.

![Приложение Azure после включения Code First Migration](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Все имеющиеся элементы списка дел по-прежнему отображаются. При повторной публикации приложения ASP.NET Core существующие данные в Базе данных SQL не теряются. Кроме того, Entity Framework Core Migrations изменяет только схему данных, оставляя существующие данные нетронутыми.

## <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики

При запуске приложения ASP.NET Core в Службе приложений Azure можно передавать журналы консоли в Cloud Shell. Таким образом, вы будете получать те же диагностические сообщения, которые помогут устранить ошибки приложения.

В примере проекта уже выполнены инструкции из руководства [Ведение журналов в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) и внесены два изменения в настройки:

- в файле *DotNetCoreSqlDb.csproj* содержится ссылка на `Microsoft.Extensions.Logging.AzureAppServices`.
- Вызовы `loggerFactory.AddAzureWebAppDiagnostics()` в *Program.cs*.

Чтобы в Службе приложений для [уровня ведения журнала](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) ASP.NET Core задать значение `Information` вместо значения по умолчанию `Error`, используйте команду [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) в Cloud Shell.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> Для уровня ведения журнала проекта уже задано значение `Information` в файле *appsettings.json*.
> 

Чтобы настроить потоки для журналов, выполните команду [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) в Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

После настройки потоков обновите приложение Azure в браузере, чтобы получить немного трафика. Вы должны увидеть, что журналы консоли теперь направляются в терминал. Если журналы консоли не отображаются, проверьте еще раз через 30 секунд.

Чтобы остановить потоковую передачу журналов, нажмите клавиши `Ctrl`+`C`.

Дополнительные сведения о настройке журналов ASP.NET Core см. в статье [Ведение журналов в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Управление приложением Azure

Перейдите на [портал Azure](https://portal.azure.com), чтобы увидеть созданное приложение.

В меню слева щелкните **Службы приложений**, а затем — имя своего приложения Azure.

![Переход к приложению Azure на портале](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

По умолчанию на портале отображается страница **Обзор** приложения. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. На вкладках в левой части страницы отображаются различные страницы конфигурации, которые можно открыть.

![Страница службы приложений на портале Azure](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Дальнейшие действия

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
