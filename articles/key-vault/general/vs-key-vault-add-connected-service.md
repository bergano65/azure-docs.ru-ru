---
title: Добавление поддержки Key Vault в проект ASP.NET с помощью Visual Studio — Azure Key Vault | Документация Майкрософт
description: Это руководство поможет вам узнать, как добавить поддержку Key Vault в веб-приложение ASP.NET или ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 68f8607db25b70ea5e6bfe21a7d6e002aeb127d6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429776"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Добавление хранилища ключей в веб-приложение с помощью функции "Подключенные службы" в Visual Studio

В этом руководстве вы узнаете, как легко добавить все, что нужно, чтобы приступить к использованию Azure Key Vault для управления секретами веб-проектов в Visual Studio при работе с проектом ASP.NET Core или проектом ASP.NET любого типа. Используя функцию Connected Services в Visual Studio, вы можете автоматически добавить Visual Studio все пакеты nuGet и настройки конфигурации, необходимые для подключения к Key Vault в Azure.

Подробные сведения об изменениях, вносимых функцией "Подключенные службы" в проект для поддержки Key Vault, можно получить из раздела [Что произошло с моим проектом ASP.NET при добавлении подключенной службы Key Vault в Visual Studio?](#how-your-aspnet-framework-project-is-modified) или [Что произошло с моим проектом ASP.NET Core при добавлении подключенной службы Key Vault в Visual Studio?](#how-your-aspnet-core-project-is-modified)

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure**. Если у вас нет подписки, зарегистрируйтесь на [бесплатную учетную запись.](https://azure.microsoft.com/pricing/free-trial/)
- **Visual Studio 2019 версия 16.3** или более поздно, или **Visual Studio 2017 версия 15.7** с установленной рабочей нагрузкой **веб-разработки.** [Скачайте это приложение](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Для ASP.NET (не Core) с Visual Studio 2017 вам нужен .NET Framework 4.7.1 или более поздние инструменты разработки, которые не установлены по умолчанию. Чтобы установить их, запустите Visual Studio Installer, щелкните **Изменить** и выберите **Отдельные компоненты**. Затем справа разверните элемент **ASP.NET и разработка веб-приложений** и выберите **Средства разработки для платформы .NET Framework 4.7.1**.
- Открытый ASP.NET 4.7.1 или позже, или ASP.NET Core 2.0 или более поздний веб-проект.

## <a name="add-key-vault-support-to-your-project"></a>Добавление поддержки Key Vault в проект

Прежде чем начать, убедитесь, что вы подписаны в Visual Studio. Восприкажитесь в той же учетной записи, что и для подписки Azure. Затем откройте ASP.NET 4.7.1 или позже, или ASP.NET веб-проектCore 2.0, и сделайте последующие шаги:

1. В **Solution Explorer**щелкните по правому щелкнутому проект, к которого вы хотите добавить поддержку Key Vault, и выберите **добавленную** > **службу Connected**Service.
   Откроется страница "Подключенная служба" с перечнем служб, которые можно добавить в проект.
1. В меню доступных служб выберите **Безопасно храните секреты в Azure Key Vault**.

   ![Выбор пункта "Безопасно храните секреты в Azure Key Vault"](../media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Выберите подписку, которая требуется использовать, а затем выберите новое или существующее Хранилище ключей. Если вы выберете новое Убежище ключа, появится ссылка **на edit.** Выберите его для настройки нового Ключевого Убежища.

   ![Выберите свою подписку.](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. В **Edit Azure Key Vault**введите имя, которое вы хотите использовать для Убежища ключей.

1. Выберите существующую **группу ресурсов**или создайте новую группу с автоматически генерируемым уникальным именем.  Если требуется создать новую группу с другим именем, можно использовать [портал Azure,](https://portal.azure.com)а затем закрыть страницу и перезагрузить список групп ресурсов.
1. Выберите **место,** в котором можно создать Убежище ключей. Если ваше веб-приложение размещено в Azure, выберите регион, в котором размещается это веб-приложение, чтобы обеспечить оптимальную производительность.
1. Выберите **уровень ценообразования.** Дополнительные сведения см. в разделе [Цены на Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Выберите **OK,** чтобы принять выбор конфигурации.
1. После выбора существующего Убежища ключей или настройки нового Убежища ключей во вкладке **Azure Key Vault** Visual Studio выберите **добавить,** чтобы добавить подключенную службу.
1. Выберите **секреты управления, хранящиеся в этой** ссылке Key Vault, чтобы открыть страницу **Секреты** для вашего Убежища ключей. Если вы закрыли страницу или проект, вы можете перейти к ней на [портале Azure,](https://portal.azure.com) выбрав **все службы** и, в соответствии с **безопасностью,** выбрав **Key Vault,** затем выберите Key Vault.
1. В разделе Key Vault для создаваемого вами Убежища ключей выберите **Секреты,** затем **создайте/импортируйте.**

   ![Создание и импорт секрета](../media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Введите секрет, например *MySecret,* и придайте ему любое значение строки в качестве теста, а затем выберите кнопку **«Создание».**

   ![Создание секрета](../media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (Необязательно.) Введите еще один секрет, но на этот раз поместите его в категорию, присвоив ему имя *Secrets--MySecret*. Этот синтаксис определяет категорию "Секреты", которая содержит секрет "MySecret".

Теперь можно обращаться к секретам в коде. Дальнейшие действия отличаются в зависимости от того, используется ASP.NET 4.7.1 или ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Доступ к секретам в коде (ASP.NET Core)

1. В Solution Explorer нажмите правой кнопкой мыши на ваш проект и выберите **пакеты Управления NuGet.** Во вкладке **«Просмотр»** найдите и установите эти два пакета NuGet: [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) и для .NET Core 2, добавьте [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) или для .NET Core 3, добавьте[Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core).

1. Для .NET Core 2 `Program.cs` выберите `BuildWebHost` вкладку и измените определение в классе Программы на следующее:

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   Для .NET Core 3 используйте следующий код.

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. Затем откройте один из файлов страницы, например *Index.cshtml.cs* и напишите следующий код:
   1. Включите `Microsoft.Extensions.Configuration` ссылку на эту директиву:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Добавьте переменную конфигурации.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. Добавьте этот конструктор или замените существующий конструктор на это:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Внесите изменения в метод `OnGet`. Обновите значение заполнителя, указанное здесь, с помощью секретного имени, созданного вами в вышеуказанных командах.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Чтобы подтвердить значение во время выполнения, `ViewData["Message"]` добавьте код для отображения в файл *shtml для* отображения секрета в сообщении.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Вы можете запустить приложение локально, чтобы убедиться, что секрет получен успешно из Убежища ключа.

## <a name="access-your-secrets-aspnet"></a>Доступ к секретам (ASP.NET)

Можно настроить конфигурацию таким образом, чтобы файл web.config `appSettings` имеет фиктивное значение в элементе, который заменяется истинным значением во время выполнения. Затем вы можете получить `ConfigurationManager.AppSettings` доступ к этому через структуру данных.

1. Отоверьте файл web..config.  Найдите тег appSettings, `configBuilders="AzureKeyVault"`добавьте атрибут и добавьте строку:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. Отобразите `About` метод в *HomeController.cs,* чтобы отобразить значение для подтверждения.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Запустите приложение локально под отладчиком, переключитесь на вкладку **About** и убедитесь, что значение из Убежища ключа отображается.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов, если она больше не нужна. При этому будут удалены хранилище ключей и связанные ресурсы. Чтобы удалить группу ресурсов на портале, сделайте следующее:

1. В поле поиска в верхней части портала введите имя группы ресурсов. Если в результатах поиска отображается группа ресурсов, используемая в этом кратком руководстве, выберите ее.
2. Выберите **Удалить группу ресурсов**.
3. В **TYPE THE RESOURCE GROUP NAME:** поле, введите имя группы ресурсов и выберите **Удалить**.

## <a name="troubleshooting"></a>Устранение неполадок

Если ваш Ключевой Vault работает на другой учетной записи Майкрософт, чем тот, который вы вошли в Visual Studio (например, Key Vault работает на вашей учетной записи работы, но Visual Studio использует вашу частную учетную запись), вы получаете ошибку в Program.cs файле, что Visual Studio не может получить доступ к Key Vault. Чтобы устранить эту проблему:

1. Перейдите на [портал Azure](https://portal.azure.com) и откройте key Vault.

1. Выберите **политики доступа,** затем **добавьте политику доступа**и выберите учетную запись, в которая вы вошли в систему в качестве основного принципала.

1. В Visual Studio выберите**настройки учетной записи** **файлов.** > 
Выберите **Добавить учетную запись** из раздела **«Все счета».** Восприкажитесь в учетной записи, выбранной вами в качестве директора политики доступа.

1. Выберите **параметры инструментов** > **Options**и ищите **аутентификацию службы Azure.** Затем выберите учетную запись, которая только что была добавлена в Visual Studio.

Теперь, когда вы отлажаете приложение, Visual Studio подключается к учетной записи, на ней находится ваш ключ Vault.

## <a name="how-your-aspnet-core-project-is-modified"></a>Как изменяется ваш проект ASP.NET Core

В этом разделе определяются точные изменения, внесенные в проект ASP.NET при добавлении службы подключения Key Vault с помощью Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Добавлены ссылки для ASP.NET Core

Влияет на файл проекта .NET ссылки и ссылки пакета NuGet.

| Тип | Справочник |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Добавлены файлы для ASP.NET Ядра

- `ConnectedService.json`добавлено, что записывает некоторую информацию о поставщике подключенных услуг, версии и ссылке документации.

### <a name="project-file-changes-for-aspnet-core"></a>Изменения файла проекта для ASP.NET Core

- Добавлены Элементная группа `ConnectedServices.json` подключенных служб и файл.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.json изменения для ASP.NET Core

- В профиль IIS Express и профиль, который соответствует имени вашего веб-проекта, добавлены следующие записи переменных среды.

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Изменения на Azure для ASP.NET Core

- Создана группа ресурсов или использована существующая.
- В указанной группе ресурсов создано хранилище ключей.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Как изменяется проект ASP.NET Framework

В этом разделе определяются точные изменения, внесенные в проект ASP.NET при добавлении службы подключения Key Vault с помощью Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Добавлены ссылки для рамочной ASP.NET

Влияет на файл проекта .NET `packages.config` ссылки и (NuGet ссылки).

| Тип | Справочник |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Добавлены файлы для ASP.NET Framework

- `ConnectedService.json`добавлено, что записывает некоторую информацию о поставщике подключенных услуг, версии и ссылку на документацию.

### <a name="project-file-changes-for-aspnet-framework"></a>Изменения файла проекта для ASP.NET Framework

- Добавлены элемент ItemGroup "Подключенные службы" и файл ConnectedServices.json.
- Ссылки на сборки .NET, описанные в разделе [Добавленные ссылки](#added-references-for-aspnet-framework).

### <a name="webconfig-or-appconfig-changes"></a>Изменения в файлах web.config или app.config

- Добавлены следующие записи конфигурации:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>Изменения в Azure для ASP.NET framework

- Создана группа ресурсов или использована существующая.
- В указанной группе ресурсов создано хранилище ключей.

## <a name="next-steps"></a>Дальнейшие действия

Если вы следовали этому учебнику, ваши разрешения Key Vault настроены для выполнения собственной подписки Azure, но это может оказаться нежелательным для производственного сценария. Можно создать управляемый итог для управления доступом к Key Vault для вашего приложения. Смотрите [Предоставить key Vault аутентификации с управляемой идентификации](/azure/key-vault/managed-identity).

Узнайте больше о разработке Key Vault, прочитав [руководство разработчика Key Vault.](developers-guide.md)
