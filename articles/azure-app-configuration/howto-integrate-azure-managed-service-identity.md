---
title: Интеграция с управляемыми удостоверениями Azure | Документация Майкрософт
description: Узнайте, как использовать управляемые удостоверения Azure для проверки подлинности с помощью и получения доступа к конфигурации приложений Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 3a5517c31cdac0bf6f5ea386a8614d15521d4479
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035536"
---
# <a name="integrate-with-azure-managed-identities"></a>интеграции с управляемыми удостоверениями Azure

[Управляемые удостоверения](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Azure Active Directory помогают упростить управление секретами для вашего облачного приложения. С помощью управляемого удостоверения можно настроить код для использования субъекта-службы, созданного для службы вычислений Azure, на которой она выполняется. Вы используете управляемое удостоверение, а не отдельные учетные данные, хранящиеся в Azure Key Vault, или локальную строку подключения. 

Служба "Конфигурация приложений Azure" и ее клиентские библиотеки для .NET Core, .NET и Java Spring имеют встроенную поддержку управляемого удостоверения службы (MSI). Хотя использовать MSI необязательно, это устраняет потребность в маркере доступа, который содержит секреты. Код может получить доступ к хранилищу конфигураций приложений, используя только конечную точку службы. Этот URL-адрес можно внедрить непосредственно в код, не беспокоясь о раскрытии любых секретов.

В этом руководстве описывается, как использовать MSI для доступа к службе "Конфигурация приложений". При этом в качестве основы используется код веб-приложения, представленный в кратких руководствах. Прежде чем продолжить, ознакомьтесь со статьей [Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"](./quickstart-aspnet-core-app.md).

Кроме того, в этом руководстве описывается, как можно использовать MSI в сочетании со ссылками на Key Vault конфигурации приложения. Это позволяет легко получить доступ к секретам, хранящимся в Key Vault, а также значениях конфигурации в конфигурации приложения. Если вы хотите изучить эту возможность, то сначала [используйте Key Vault ссылки с ASP.NET Core](./use-key-vault-references-dotnet-core.md) .

Вы можете выполнять шаги в этом учебнике с помощью любого редактора кода. [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, который доступен на платформах Windows, macOS и Linux.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * предоставление доступа к службе "Конфигурация приложений" с помощью управляемого удостоверения;
> * настройка приложения на использование управляемого удостоверения при подключении к службе "Конфигурация приложений".
> * При необходимости настройте приложение для использования управляемого удостоверения при подключении к Key Vault через конфигурацию приложения Key Vault ссылке.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством требуется:

* [пакет SDK для .NET Core](https://www.microsoft.com/net/download/windows);
* [настроенный Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Добавление управляемого удостоверения

Чтобы настроить управляемое удостоверение на портале, сначала необходимо создать обычное приложение, а затем активировать соответствующую функцию.

1. Создайте экземпляр служб приложений в [портал Azure](https://portal.azure.com) , как обычно. Перейдите к нему на портале.

2. Прокрутите вниз до группы **параметров** в левой панели и выберите **Удостоверение**.

3. На вкладке **Назначено системой** для параметра **Состояние** установите значение **Вкл.** и выберите **Сохранить**.

4. Ответьте **Да** при появлении запроса на включение управляемого удостоверения, назначенного системой.

    ![Настройка управляемого удостоверения в службе приложений](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Предоставление доступа к службе "Конфигурация приложений"

1. На [портале Azure](https://portal.azure.com) щелкните **Все ресурсы** и выберите хранилище конфигураций приложений, которое вы создали по инструкциям из краткого руководства.

1. Выберите **Управление доступом (IAM)** .

1. На вкладке **Проверить доступ** щелкните **Добавить** в пользовательском интерфейсе карточки **Добавить назначение роли**.

1. В поле **Роль** выберите **Участник**. В поле **Назначение доступа к** задайте значение **Служба приложений** (в разделе **Управляемое удостоверение, назначаемое системой**).

1. В поле **Подписка** выберите подписку Azure. Выберите ресурс Службы приложений для своего приложения.

1. Щелкните **Сохранить**.

    ![Добавление управляемого удостоверения](./media/add-managed-identity.png)

1. Необязательно: Если вы хотите предоставить доступ к Key Vault, следуйте указаниям в [подKey Vault аутентификации с помощью управляемого удостоверения](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Использование управляемого удостоверения

1. Найдите URL-адрес хранилища конфигурации приложения, перейдя на экран настройки в портал Azure, а затем перейдя на вкладку **ключи доступа** .

1. Откройте *appsettings.json* и добавьте следующий скрипт. Замените *\<service_endpoint >* , включая квадратные скобки, на URL-адрес хранилища конфигурации приложения. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Если вы хотите получить доступ только к значениям, хранящимся непосредственно в конфигурации приложения, откройте *Program.CS*и обновите метод `CreateWebHostBuilder`, заменив метод `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

1. Если вы хотите использовать значения конфигурации приложения, а также ссылки Key Vault, откройте *Program.CS*и обновите метод `CreateWebHostBuilder`, как показано ниже. При этом создается новый `KeyVaultClient` с использованием `AzureServiceTokenProvider` и передает эту ссылку вызову метода `UseAzureKeyVault`.

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                    KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                    
                    config.AddAzureAppConfiguration(options => options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"])).UseAzureKeyVault(kvClient));
                })
                .UseStartup<Startup>();
    ```

    Теперь доступ к Key Vault ссылкам можно получить так же, как и к любому другому ключу конфигурации приложения. Поставщик конфигурации будет использовать `KeyVaultClient`, настроенный для проверки подлинности в Key Vault и получения значения.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Развертывание из локального репозитория Git

Включить локальное развертывание Git для вашего приложения с сервера сборки Kudu проще всего с помощью Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Включить локальный Git с помощью Kudu
Если у вас нет локального репозитория Git для приложения, необходимо его инициализировать. Для этого выполните следующие команды в каталоге проекта приложения:

```cmd
git init
git add .
git commit -m "Initial version"
```

Чтобы включить локальное развертывание Git для вашего приложения с сервера сборки Kudu, выполните [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) в Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Чтобы вместо этого создать приложение с поддержкой Git, выполните команду [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) в Cloud Shell с параметром `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Команда `az webapp create` предоставляет примерно следующие выходные данные:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Развертывание проекта

Вернитесь к _окну терминала (в локальном расположении)_ и добавьте удаленное приложение Azure в локальный репозиторий Git. Замените _\<url>_ URL-адресом удаленного репозитория Git, полученным на шаге [Включение использования репозитория Git для приложения](#enable-local-git-with-kudu).

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

Поставщики службы "Конфигурация приложений" для платформы .NET Framework и Java Spring также имеют встроенную поддержку управляемых удостоверений. В таких случаях при настройке поставщика вы можете использовать конечную точку URL-адреса для хранилища конфигураций приложений, а не указывать строку подключения целиком. Например, для консольного приложения .NET Framework, созданного по инструкциям из краткого руководства, нужно указать следующие параметры в файле *App.config*:

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

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Примеры интерфейса командной строки](./cli-samples.md)
