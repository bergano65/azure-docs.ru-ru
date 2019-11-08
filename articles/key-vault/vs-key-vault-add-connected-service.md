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
ms.openlocfilehash: 9331f13bd85d9df0d47f8fa9d0964974764691f7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73815106"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Добавление хранилища ключей в веб-приложение с помощью функции "Подключенные службы" в Visual Studio

В этом руководстве вы узнаете, как легко добавить все, что нужно, чтобы приступить к использованию Azure Key Vault для управления секретами веб-проектов в Visual Studio при работе с проектом ASP.NET Core или проектом ASP.NET любого типа. С помощью функции Подключенные службы в Visual Studio можно автоматически добавить в Visual Studio все пакеты NuGet и параметры конфигурации, необходимые для подключения к Key Vault в Azure.

Подробные сведения об изменениях, вносимых функцией "Подключенные службы" в проект для поддержки Key Vault, можно получить из раздела [Что произошло с моим проектом ASP.NET при добавлении подключенной службы Key Vault в Visual Studio?](#how-your-aspnet-framework-project-is-modified) или [Что произошло с моим проектом ASP.NET Core при добавлении подключенной службы Key Vault в Visual Studio?](#how-your-aspnet-core-project-is-modified)

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure**. Если у вас нет подписки, зарегистрируйтесь, чтобы получить [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
- **Visual studio 2019 версии 16,3 Предварительная версия 1** или более поздней или **Visual studio 2017 версии 15,7** с установленной рабочей нагрузкой **веб-разработки** . [Скачайте это приложение](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Для ASP.NET (не Core) с Visual Studio 2017 вам потребуются средства разработки .NET Framework 4.7.1 или более поздней версии, которые не устанавливаются по умолчанию. Чтобы установить их, запустите Visual Studio Installer, щелкните **Изменить** и выберите **Отдельные компоненты**. Затем справа разверните элемент **ASP.NET и разработка веб-приложений** и выберите **Средства разработки для платформы .NET Framework 4.7.1**.
- Открыт веб-проект ASP.NET 4.7.1 или более поздней версии или ASP.NET Core 2,0.

## <a name="add-key-vault-support-to-your-project"></a>Добавление поддержки Key Vault в проект

Прежде чем начать, убедитесь, что вы вошли в Visual Studio. Войдите, используя ту же учетную запись, которая используется для подписки Azure. Затем откройте веб-проект ASP.NET 4.7.1 или более поздней версии или ASP.NET Core 2,0 и выполните следующие действия.

1. В **Обозреватель решений**щелкните правой кнопкой мыши проект, для которого требуется добавить поддержку хранилища ключей, и выберите пункт **Добавить** > **подключенную службу**.
   Откроется страница "Подключенная служба" с перечнем служб, которые можно добавить в проект.
1. В меню доступных служб выберите **Безопасно храните секреты в Azure Key Vault**.

   ![Выбор пункта "Безопасно храните секреты в Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Выберите подписку, которую хотите использовать, а затем выберите новую или существующую Key Vault. При выборе нового Key Vault появляется ссылка Edit ( **изменить** ). Выберите его, чтобы настроить новый Key Vault.

   ![Выберите свою подписку.](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. В поле **изменить Azure Key Vault**введите имя, которое будет использоваться для Key Vault.

1. Выберите существующую **группу ресурсов**или создайте новую, используя автоматически созданное уникальное имя.  Если вы хотите создать новую группу с другим именем, можно использовать [портал Azure](https://portal.azure.com), а затем закрыть страницу и перезапустить ее, чтобы перезагрузить список групп ресурсов.
1. Выберите **Расположение** для создания Key Vault. Если ваше веб-приложение размещено в Azure, выберите регион, в котором размещается это веб-приложение, чтобы обеспечить оптимальную производительность.
1. Выберите **ценовую категорию**. Дополнительные сведения см. в разделе [Цены на Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Нажмите кнопку **ОК** , чтобы принять параметры конфигурации.
1. После выбора существующего Key Vault или настройки нового Key Vault на вкладке **Azure Key Vault** Visual Studio нажмите кнопку **Добавить** , чтобы добавить подключенную службу.
1. Выберите ссылку **Управление секретами, хранящимися в этой Key Vault** , чтобы открыть страницу **секреты** для Key Vault. Если вы закрыли страницу или проект, перейдите к ней в [портал Azure](https://portal.azure.com) , выбрав **все службы** и в разделе **Безопасность**, выбрав **Key Vault**, а затем выберите Key Vault.
1. В разделе "Key Vault" для созданного вами хранилища ключей выберите **Секреты**, затем выберите **Generate/Import** (Создать и импортировать).

   ![Создание и импорт секрета](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Введите секрет, например *mysecret* , и присвойте ему любое строковое значение в качестве теста, а затем нажмите кнопку **создать** .

   ![Создание секрета](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (Необязательно.) Введите еще один секрет, но на этот раз поместите его в категорию, присвоив ему имя *Secrets--MySecret*. Этот синтаксис указывает категорию "секреты", которая содержит секрет "MySecret".

Теперь можно обращаться к секретам в коде. Дальнейшие действия отличаются в зависимости от того, используется ASP.NET 4.7.1 или ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>Получите доступ к секретам в коде

1. В обозреватель решений щелкните правой кнопкой мыши проект и выберите **Управление пакетами NuGet**. На вкладке **Обзор** найдите и установите следующие два пакета NuGet: [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) и [Microsoft. Azure. KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

1. Перейдите на вкладку `Program.cs` и замените класс Program следующим кодом:

   ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
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
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

1. Затем откройте файл `About.cshtml.cs` и напишите следующий код:
   1. Включите ссылку на `Microsoft.Extensions.Configuration` с помощью этой инструкции using:

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. Добавьте этот конструктор:

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Обновите метод `OnGet`. Обновите значение заполнителя, показанное здесь, на имя секрета, созданного в приведенных выше командах.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Запустите приложение локально, перейдя на страницу About. Вы должны увидеть полученное значение секрета.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов, если она больше не нужна. При этому будут удалены хранилище ключей и связанные ресурсы. Чтобы удалить группу ресурсов на портале, сделайте следующее:

1. В поле поиска в верхней части портала введите имя группы ресурсов. Если в результатах поиска отображается группа ресурсов, используемая в этом кратком руководстве, выберите ее.
2. Выберите **Удалить группу ресурсов**.
3. В поле **введите имя группы ресурсов:** введите имя группы ресурсов и выберите **Удалить**.

## <a name="how-your-aspnet-core-project-is-modified"></a>Изменение проекта ASP.NET Core

В этом разделе указаны точные изменения, внесенные в проект ASP.NET при добавлении подключенной службы Key Vault с помощью Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Добавлены ссылки на ASP.NET Core

Влияет на ссылки .NET на файл проекта и на ссылки на пакет NuGet.

| Тип | Справочные материалы |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Добавлены файлы для ASP.NET Core

- Добавлен `ConnectedService.json`, в котором записываются сведения о поставщике подключенной службы, версии и ссылка на документацию.

### <a name="project-file-changes-for-aspnet-core"></a>Изменения в файле проекта для ASP.NET Core

- Добавлены Подключенные службы ItemGroup и `ConnectedServices.json` файл.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>изменения launchsettings. JSON для ASP.NET Core

- В профиль IIS Express и профиль, который соответствует имени вашего веб-проекта, добавлены следующие записи переменных среды.

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Изменения в Azure для ASP.NET Core

- Создана группа ресурсов или использована существующая.
- В указанной группе ресурсов создано хранилище ключей.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Изменение проекта ASP.NET Framework

В этом разделе указаны точные изменения, внесенные в проект ASP.NET при добавлении подключенной службы Key Vault с помощью Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Добавлены ссылки для ASP.NET Framework

Влияет на ссылки и `packages.config` в файле проекта .NET (ссылки NuGet).

| Тип | Справочные материалы |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Добавлены файлы для ASP.NET Framework

- `ConnectedService.json` добавлен, в котором записываются сведения о поставщике подключенной службы, версии и ссылке на документацию.

### <a name="project-file-changes-for-aspnet-framework"></a>Изменения в файле проекта для ASP.NET Framework

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

### <a name="changes-on-azure-for-aspnet-framework"></a>Изменения в Azure для ASP.NET Framework

- Создана группа ресурсов или использована существующая.
- В указанной группе ресурсов создано хранилище ключей.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Key Vault разработке см. в разделе [руководств разработчика Key Vault](key-vault-developers-guide.md).
