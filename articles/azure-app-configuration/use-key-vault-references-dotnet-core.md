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
ms.openlocfilehash: 992cface653bf3fe52afc7efa3f17573fcf91399
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469647"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Руководство по использованию ссылок Key Vault в приложении ASP.NET Core

В этом учебнике вы узнаете, как использовать службу "Конфигурация приложений Azure" с Azure Key Vault. Конфигурация приложений Azure и Key Vault — это дополняющие службы, которые используются параллельно в большинстве развертываний приложений.

Служба "Конфигурация приложений Azure" упрощает их использование, позволяя создавать ключи, ссылающиеся на значения, хранящиеся в Key Vault. Когда служба "Конфигурация приложений Azure" создает такие ключи, она сохраняет URI значений Key Vault, а не сами значения.

С помощью поставщика клиента "Конфигурация приложений Azure" ваше приложение получает ссылки на Key Vault так же, как и любые другие ключи, хранящиеся в службе "Конфигурация приложений Azure". В этом случае значения, хранящиеся в службе "Конфигурация приложений Azure", являются URI, которые ссылаются на значения в Key Vault. Это не значения или учетные данные Key Vault. Так как поставщик клиента распознает ключи как ссылки на Key Vault, для получения их значений он использует Key Vault.

Приложение отвечает за правильную проверку подлинности как в службе "Конфигурация приложений", так и в Key Vault. Две службы не обмениваются данными напрямую.

В этом учебнике показано, как можно реализовать ссылки на Key Vault в коде. При этом в качестве основы используется код веб-приложения, представленный в кратких руководствах. Прежде чем продолжить, ознакомьтесь со статьей [Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"](./quickstart-aspnet-core-app.md).

Вы можете выполнять шаги в этом учебнике с помощью любого редактора кода. Например, [Visual Studio Code](https://code.visualstudio.com/) — это кроссплатформенный редактор кода, доступный для операционных систем Windows, macOS и Linux.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание ключа службы "Конфигурация приложений Azure", который ссылается на значение, хранящееся в Key Vault;
> * доступ к значению этого ключа из веб-приложения ASP.NET Core.

## <a name="prerequisites"></a>Предварительные требования

Перед работой с этим учебником установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Создание хранилища

1. Выберите параметр **Создать ресурс** в верхнем левом углу окна портала Azure.

    ![Выходные данные после создания Key Vault](./media/quickstarts/search-services.png)
1. В поле поиска введите **Key Vault**.
1. В списке результатов выберите **Key Vault** слева.
1. В разделе **Хранилища ключей** выберите **Добавить**.
1. Справа в разделе **Создать Key Vault** введите приведенные ниже сведения.
    - Выберите **Подписка**, чтобы выбрать подписку.
    - В разделе **Группа ресурсов** выберите **Создать** и введите имя группы ресурсов.
    - В поле **Имя Key Vault** укажите уникальное имя. Для работы с этим учебником введите **Contoso-vault2**.
    - В раскрывающемся списке **Регион** выберите расположение.
1. Для других свойств раздела **создания Key Vault** оставьте значения по умолчанию.
1. Нажмите кнопку **Создать**.

На этом этапе доступ к этому новому хранилищу может получать только учетная запись Azure.

![Выходные данные после создания Key Vault](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Добавление секрета в Key Vault

Чтобы добавить секрет в хранилище, вам просто нужно выполнить несколько дополнительных шагов. В этом случае мы добавим сообщение, которое можно использовать для проверки возвращения Key Vault. Сообщение называется **Message**, и в нем сохраняется значение "Hello from Key Vault".

1. На странице свойств Key Vault выберите **Секреты**.
1. Выберите **Создать или импортировать**.
1. В области **Создание секрета** выберите следующие значения:
    - **Параметры отправки.** Введите **Вручную**.
    - **Имя.** Введите **Message**.
    - **Value** (Значение): Введите **Hello from Key Vault**.
1. Для других свойств раздела **Создание секрета** оставьте значения по умолчанию.
1. Нажмите кнопку **Создать**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Добавление ссылки на Key Vault в службу "Конфигурация приложений Azure"

1. Войдите на [портале Azure](https://portal.azure.com). Щелкните **Все ресурсы** и выберите экземпляр хранилища "Конфигурация приложений Azure", который вы создали по инструкциям из краткого руководства.

1. Выберите **Обозреватель конфигураций**.

1. Выберите **+ Создать** > **Ссылка на хранилище ключей** и укажите следующие значения:
    - **Ключ**: Выберите **TestApp:Settings:KeyVaultMessage**.
    - **Метка** — оставьте это значение пустым.
    - **Подписка**, **Группа ресурсов** и **Хранилище ключей**: введите значения, соответствующие значениям хранилища ключей, созданного в предыдущем разделе.
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

    ```
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Добавьте секреты для *clientId* и *clientSecret* в диспетчер секретов. Это средство для хранения конфиденциальных данных, добавленных в файл *CSPROJ* при прохождении руководства [Краткое руководство. Создание приложения ASP.NET Core с помощью службы "Конфигурация приложений Azure"](./quickstart-aspnet-core-app.md). Эти команды должны выполняться в том же каталоге, что и файл *CSPROJ*.

    ```
    dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>
    dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>
    ```

> [!NOTE]
> Эти учетные данные Key Vault используются только в вашем приложении. Приложение выполняет проверку подлинности непосредственно в Key Vault с использованием этих учетных данных. Они никогда не передаются в службу настройки приложений.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Обновление кода для использования ссылки Key Vault

1. Откройте файл *Program.cs* и добавьте ссылки на приведенные ниже необходимые пакеты.

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Обновите метод `CreateWebHostBuilder`, чтобы использовать службу "Конфигурация приложений", путем вызова метода `config.AddAzureAppConfiguration`. Добавьте параметр `UseAzureKeyVault`, чтобы передать в Key Vault новую ссылку `KeyVaultClient`.

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

1. При инициализации подключения к службе "Конфигурация приложений Azure" вы передали ссылку `KeyVaultClient` в метод `UseAzureKeyVault`. После инициализации можно получить доступ к значениям ссылок на Key Vault так же, как и к значениям обычных ключей "Конфигурация приложений Azure".

    Чтобы увидеть этот процесс в действии, откройте файл *Index.cshtml* в папке **Представления** > **Главная страница**. Замените содержимое файла приведенным ниже кодом.

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

    Вы получаете доступ к значению ссылки на Key Vault **TestApp:Settings:KeyVaultMessage** так же, как и к значению конфигурации **TestApp:Settings:Message**.

## <a name="build-and-run-the-app-locally"></a>Создание и запуск приложения локально

1. Чтобы создать приложение с помощью .NET Core CLI, выполните следующую команду в оболочке командной строки:

    ```
    dotnet build
    ```

1. Когда создание завершится, запустите веб-приложение локально с помощью следующей команды:

    ```
    dotnet run
    ```

1. Откройте окно браузера и перейдите по адресу `http://localhost:5000`, который является URL-адресом по умолчанию для веб-приложения, размещенного локально.

    ![Краткое руководство по запуску приложения, размещенного локально](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом учебнике вы создали ключ службы "Конфигурация приложений Azure", который ссылается на значение, хранящееся в Key Vault. Чтобы узнать, как добавить удостоверение службы, управляемое Azure, которое упрощает доступ к службе "Конфигурация приложения Azure" и Key Vault, перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Руководство по интеграции с управляемыми удостоверениями Azure](./howto-integrate-azure-managed-service-identity.md)
