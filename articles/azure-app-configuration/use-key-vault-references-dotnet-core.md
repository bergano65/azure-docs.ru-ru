---
title: Учебник по использованию ссылок Key Vault службы "Конфигурация приложений Azure" в приложении ASP.NET Core | Документация Майкрософт
description: В этом учебнике описано, как использовать ссылки Key Vault службы "Конфигурация приложений Azure" из приложения ASP.NET Core.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035819"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Руководство по использованию ссылок Key Vault в приложении ASP.NET Core

В этом учебнике вы узнаете, как использовать службу "Конфигурация приложений Azure" с Azure Key Vault. Это дополняющие службы, которые используются параллельно в большинстве развертываний приложений. Чтобы упростить их использование, служба "Конфигурация приложений" позволяет создавать ключи, ссылающиеся на значения, хранящиеся в Key Vault. При этом в службе "Конфигурация приложений" сохраняется URI значения Key Vault, а не само значение. Приложение получает значение этого ключа с помощью поставщика клиента службы "Конфигурация приложений", как и любой другой ключ, хранящийся в этой службе. Поставщик клиента распознает его как ссылку Key Vault и обращается к Key Vault для получения значения. Приложение отвечает за правильную проверку подлинности как в службе "Конфигурация приложений", так и в Key Vault. Две службы не обмениваются данными напрямую.

В этом учебнике показано, как можно реализовать ссылки Key Vault в коде. При этом в качестве основы используется код веб-приложения, представленный в кратких руководствах. Прежде чем продолжить, ознакомьтесь со статьей [Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"](./quickstart-aspnet-core-app.md).

Вы можете выполнять шаги в этом учебнике с помощью любого редактора кода. [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, который доступен на платформах Windows, macOS и Linux.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание ключа службы "Конфигурация приложений", который ссылается на значение, хранящееся в Key Vault
> * Доступ к значению этого ключа из веб-приложения ASP.NET Core

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Создание хранилища

1. Выберите параметр **Создать ресурс** в верхнем левом углу окна портала Azure.

    ![Выходные данные после создания Key Vault](./media/quickstarts/search-services.png)
2. В поле поиска введите **Key Vault**.
3. В списке результатов выберите **Key Vault**.
4. В разделе Key Vault выберите **Создать**.
5. В разделе **Создать Key Vault** введите приведенные ниже сведения.
    - **Имя.** Укажите уникальное имя. В этом кратком руководстве мы используем **Contoso-vault2**. 
    - **Подписка**: Выберите подписку.
    - В разделе **Группа ресурсов** выберите **Создать** и введите имя группы ресурсов.
    - В раскрывающемся меню **Расположение** выберите расположение.
    - Для других параметров оставьте значения по умолчанию.
6. Указав приведенные выше сведения, выберите **Создать**.

На этом этапе доступ к этому новому хранилищу может получать только учетная запись Azure.

![Выходные данные после создания Key Vault](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Добавление секрета в Key Vault

Чтобы добавить секрет в хранилище, вам просто нужно выполнить несколько дополнительных шагов. В этом случае мы добавим сообщение, которое можно использовать для проверки возвращения Key Vault. Сообщение называется **Message**, и в нем сохраняется значение **Hello from Key Vault**.

1. На странице свойств Key Vault выберите **Секреты**.
1. Щелкните **Generate/Import** (Создать или импортировать).
1. На экране **Создание секрета** выберите следующие значения:
    - **Параметры отправки.** Вручную.
    - **Имя.** Сообщение
    - **Value** (Значение): Hello from Key Vault
    - Оставьте другие значения по умолчанию. Нажмите кнопку **Создать**.

## <a name="add-a-key-vault-reference-to-app-config"></a>Добавление ссылки Key Vault в конфигурацию приложения

1. Войдите на [портале Azure](https://portal.azure.com). Щелкните **Все ресурсы** и выберите экземпляр хранилища конфигураций приложений, который вы создали по инструкциям из краткого руководства.

1. Щелкните **Explorer конфигурации**

1. Щелкните **+ Создать** > **Key vault reference** (Ссылка на хранилище ключей) и выберите следующие значения:
    - **Ключ**: TestApp:Settings:KeyVaultMessage
    - **Метка** — Не указывайте
    - **Подписка**, **Группа ресурсов**, **Хранилище ключей**: выберите параметры, соответствующие хранилищу Key Vault, созданному в предыдущем разделе.
    - **Секрет**: выберите секрет с именем **Message**, созданный в предыдущем разделе.

## <a name="connect-to-key-vault"></a>Подключение к Key Vault

1. В этом учебнике вы используете субъект-службу для проверки подлинности в KeyVault. Создайте субъект-службу с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) Azure CLI:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Эта операция возвращает ряд пар "ключ — значение". 

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Выполните следующую команду, чтобы предоставить субъекту-службе доступ к хранилищу ключей:

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. Добавьте секреты для *clientId* и *clientSecret* в диспетчер секретов. Эти команды должны выполняться в том же каталоге, что и файл *CSPROJ*.

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> Эти учетные данные Key Vault используются только в вашем приложении. Приложение выполняет проверку подлинности непосредственно в Key Vault с использованием этих учетных данных. Они никогда не передаются в службу настройки приложений.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Обновление кода для использования ссылки Key Vault

1. Откройте файл *Program.cs* и добавьте ссылки на необходимые пакеты.

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Обновите метод `CreateWebHostBuilder`, чтобы использовать службу "Конфигурация приложений", путем вызова метода `config.AddAzureAppConfiguration()`. Добавьте параметр `UseAzureKeyVault`, передав в Key Vault новую ссылку `KeyVaultClient`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. После передачи ссылки *KeyVaultClient* в метод `UseAzureKeyVault` при инициализации подключения к службе "Конфигурация приложений" можно получить доступ к значениям ссылок Key Vault так же, как и к значениям обычных ключей службы "Конфигурация приложений". Чтобы увидеть этот процесс в действии, откройте файл *Index.cshtml* в каталоге "Представления > Главная страница". Замените содержимое приведенным ниже кодом:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Вы получаете доступ к значению ссылки Key Vault *TestApp:Settings:KeyVaultMessage* так же, как и к значению конфигурации *TestApp:Settings:Message*

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Чтобы создать приложение с помощью .NET Core CLI, выполните следующую команду в оболочке командной строки:

        dotnet build

2. Когда создание завершится, запустите веб-приложение локально с помощью следующей команды:

        dotnet run

3. Откройте окно браузера и перейдите по адресу `http://localhost:5000`, который является URL-адресом по умолчанию для веб-приложения, размещенного локально.

    ![Краткое руководство. Запуск приложения, размещенного локально](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

По инструкциям из этого руководства вы добавили управляемое удостоверение службы Azure, чтобы упростить доступ к службе "Конфигурация приложений" и улучшить управление учетными данными для вашего приложения. Чтобы узнать больше об использовании службы "Конфигурация приложений", перейдите к примерам скриптов Azure CLI.

> [!div class="nextstepaction"]
> [Примеры интерфейса командной строки](./cli-samples.md)
