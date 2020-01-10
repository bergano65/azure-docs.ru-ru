---
title: интеграции с управляемыми удостоверениями Azure
description: Узнайте, как использовать управляемые удостоверения Azure для проверки подлинности с помощью и получения доступа к конфигурации приложений Azure.
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 12/29/2019
ms.author: lcozzens
ms.openlocfilehash: 7461f378a4f95a43971f5893fe70739511e942ff
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732007"
---
# <a name="integrate-with-azure-managed-identities"></a>Интеграция с управляемыми удостоверениями Azure

[Управляемые удостоверения](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Azure Active Directory помогают упростить управление секретами для вашего облачного приложения. Используя управляемое удостоверение, код может использовать субъект-службу, созданный для службы Azure, на которой он работает. Вы используете управляемое удостоверение, а не отдельные учетные данные, хранящиеся в Azure Key Vault, или локальную строку подключения. 

В конфигурации приложений Azure, а также в клиентских библиотеках .NET Core, .NET Framework и Java есть встроенная поддержка идентификации. Хотя это и не обязательно, управляемое удостоверение устраняет необходимость в маркере доступа, содержащем секреты. Код может получить доступ к хранилищу конфигураций приложений, используя только конечную точку службы. Этот URL-адрес можно внедрить непосредственно в код, не беспокоясь о раскрытии любых секретов.

В этом руководстве показано, как можно воспользоваться преимуществами управляемого удостоверения для доступа к конфигурации приложения. При этом в качестве основы используется код веб-приложения, представленный в кратких руководствах. Прежде чем продолжить, ознакомьтесь со статьей [Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"](./quickstart-aspnet-core-app.md).

В этом учебнике также показано, как можно использовать управляемое удостоверение в сочетании со ссылками на Key Vault конфигурации приложения. С одним управляемым удостоверением можно легко получить доступ к секретам из Key Vault и значений конфигурации из конфигурации приложения. Если вы хотите изучить эту возможность, то сначала [используйте Key Vault ссылки с ASP.NET Core](./use-key-vault-references-dotnet-core.md) .

Вы можете выполнять шаги в этом учебнике с помощью любого редактора кода. [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, который доступен на платформах Windows, macOS и Linux.

В этом руководстве описано следующее.

> [!div class="checklist"]
> * предоставление доступа к службе "Конфигурация приложений" с помощью управляемого удостоверения;
> * настройка приложения на использование управляемого удостоверения при подключении к службе "Конфигурация приложений".
> * При необходимости настройте приложение для использования управляемого удостоверения при подключении к Key Vault через конфигурацию приложения Key Vault ссылке.

## <a name="prerequisites"></a>Технические условия

Для работы с этим руководством требуется:

* [пакет SDK для .NET Core](https://www.microsoft.com/net/download/windows);
* [настроенный Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Добавление управляемого удостоверения

Чтобы настроить управляемое удостоверение на портале, сначала необходимо создать приложение, а затем включить эту функцию.

1. Создайте экземпляр служб приложений в [портал Azure](https://portal.azure.com) , как обычно. Перейдите к нему на портале.

1. Прокрутите вниз до группы **параметров** в левой панели и выберите **Удостоверение**.

1. На вкладке **Назначено системой** для параметра **Состояние** установите значение **Вкл.** и выберите **Сохранить**.

1. Ответьте **Да** при появлении запроса на включение управляемого удостоверения, назначенного системой.

    ![Настройка управляемого удостоверения в службе приложений](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Предоставление доступа к службе "Конфигурация приложений"

1. В [портал Azure](https://portal.azure.com)выберите **все ресурсы** и выберите хранилище конфигураций приложений, которое вы создали в кратком руководстве.

1. Выберите **Управление доступом (IAM)** .

1. На вкладке **Проверить доступ** щелкните **Добавить** в пользовательском интерфейсе карточки **Добавить назначение роли**.

1. В разделе **роль**выберите **модуль чтения данных конфигурации приложения**. В поле **Назначение доступа к** задайте значение **Служба приложений** (в разделе **Управляемое удостоверение, назначаемое системой**).

1. В поле **Подписка** выберите подписку Azure. Выберите ресурс Службы приложений для своего приложения.

1. Щелкните **Сохранить**.

    ![Добавление управляемого удостоверения](./media/add-managed-identity.png)

1. Необязательно. Если вы хотите предоставить доступ к Key Vault, следуйте указаниям в [подKey Vault аутентификации с помощью управляемого удостоверения](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Использование управляемого удостоверения

1. Добавьте ссылку на пакет *Azure. Identity* :

    ```cli
    dotnet add package Azure.Identity --version 1.1.0
    ```

1. Найдите конечную точку в хранилище конфигураций приложения. Этот URL-адрес указан на вкладке " **ключи доступа** " для магазина в портал Azure.

1. Откройте *appsettings.json* и добавьте следующий скрипт. Замените *\<service_endpoint >* , включая квадратные скобки, URL-адресом хранилища конфигурации приложения. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Откройте *Program.CS*и добавьте ссылку на пространства имен `Azure.Identity` и `Microsoft.Azure.Services.AppAuthentication`:

    ```csharp-interactive
    using Azure.Identity;
    using Microsoft.Azure.Services.AppAuthentication;
    ```

1. Если вы хотите получить доступ только к значениям, хранящимся непосредственно в конфигурации приложения, обновите метод `CreateWebHostBuilder`, заменив метод `config.AddAzureAppConfiguration()`.

    > [!IMPORTANT]
    > `CreateHostBuilder` заменяет `CreateWebHostBuilder` в .NET Core 3.0.  Выберите правильный синтаксис в зависимости от среды.

    ### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x).

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>();
    ```

    ### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```
    ---

1. Чтобы использовать значения конфигурации приложения и ссылки Key Vault, обновите *Program.CS* , как показано ниже. Этот код создает новый `KeyVaultClient` с помощью `AzureServiceTokenProvider` и передает эту ссылку вызову метода `UseAzureKeyVault`.

    ### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x).

    ```csharp
            public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                        KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                        
                        config.AddAzureAppConfiguration(options => options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()).UseAzureKeyVault(kvClient));
                    })
                    .UseStartup<Startup>();
    ```

    ### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                        AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                        KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                        
                        config.AddAzureAppConfiguration(options => options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()).UseAzureKeyVault(kvClient));
                    })
                    .UseStartup<Startup>());
    ```
    ---

    Теперь доступ к Key Vault ссылкам можно получить так же, как и к любому другому ключу конфигурации приложения. Поставщик конфигурации будет использовать `KeyVaultClient`, настроенный для проверки подлинности в Key Vault и получения значения.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Развертывание из локального репозитория Git

Самый простой способ включить локальное развертывание Git для приложения с сервером сборки KUDU — использовать [Azure Cloud Shell](https://shell.azure.com).

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Включить локальный Git с помощью Kudu
Если у вас нет локального репозитория Git для приложения, необходимо его инициализировать. Чтобы инициализировать локальный репозиторий Git, выполните следующие команды в каталоге проекта приложения:

```cmd
git init
git add .
git commit -m "Initial version"
```

Чтобы включить локальное развертывание Git для вашего приложения с сервера сборки Kudu, выполните [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) в Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Эта команда дает примерно следующий результат:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Развертывание проекта

В _окне терминала на локальном_компьютере добавьте удаленное хранилище Azure в локальный репозиторий Git. Замените _URL-адрес\<_ URL-адресом удаленного репозитория Git, полученным на странице [Включение локального репозитория Git с KUDU](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Отправьте код в удаленное приложение Azure, чтобы развернуть приложение. Когда появится запрос на ввод пароля, введите пароль, созданный в разделе [Настройка пользователя развертывания](#configure-a-deployment-user). Не используйте пароль, который вы используете для входа на портал Azure.

```bash
git push azure master
```

В выходных данных могут отображаться автоматизированные операции среды выполнения, например MSBuild для ASP.NET, `npm install` для Node.js и `pip install` для Python.

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure

Перейдите к своему веб-приложению с помощью браузера, чтобы убедиться, что содержимое развернуто.

```bash
http://<app_name>.azurewebsites.net
```

![Приложение, работающее в Службе приложений](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Использование управляемого удостоверения с другими языками

Поставщики службы "Конфигурация приложений" для платформы .NET Framework и Java Spring также имеют встроенную поддержку управляемых удостоверений. При настройке одного из этих поставщиков можно использовать конечную точку URL-адреса вашего хранилища вместо полной строки подключения. 

Например, можно обновить консольное приложение .NET Framework, созданное в кратком руководстве, чтобы указать следующие параметры в файле *app. config* :

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве вы добавили управляемое удостоверение Azure для упрощения доступа к конфигурации приложения и улучшения управления учетными данными для приложения. Чтобы узнать больше об использовании службы "Конфигурация приложений", перейдите к примерам скриптов Azure CLI.

> [!div class="nextstepaction"]
> [Примеры интерфейса командной строки](./cli-samples.md)