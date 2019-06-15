---
title: Добавление поддержки Key Vault в проект ASP.NET с помощью Visual Studio — Azure Key Vault | Документация Майкрософт
description: Это руководство поможет вам узнать, как добавить поддержку Key Vault в веб-приложение ASP.NET или ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.prod: visual-studio
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: ghogen
ms.openlocfilehash: 154eaa577ea66056c301db9516b425931b81d24d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730434"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Добавление хранилища ключей в веб-приложение с помощью функции "Подключенные службы" в Visual Studio

В этом руководстве вы узнаете, как легко добавить все, что нужно, чтобы приступить к использованию Azure Key Vault для управления секретами веб-проектов в Visual Studio при работе с проектом ASP.NET Core или проектом ASP.NET любого типа. С помощью функции подключенных служб в Visual Studio, может иметь Visual Studio автоматически добавляются все пакеты NuGet и параметры конфигурации, необходимые для подключения к хранилищу ключей в Azure. 

Подробные сведения об изменениях, вносимых функцией "Подключенные службы" в проект для поддержки Key Vault, можно получить из раздела [Что произошло с моим проектом ASP.NET при добавлении подключенной службы Key Vault в Visual Studio?](#how-your-aspnet-framework-project-is-modified) или [Что произошло с моим проектом ASP.NET Core при добавлении подключенной службы Key Vault в Visual Studio?](#how-your-aspnet-core-project-is-modified)

## <a name="prerequisites"></a>Технические условия

- **Подписка Azure**. Если у вас нет подписки, вы можете зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019** или **Visual Studio 2017 версии 15.7** с **веб-разработки** установленной рабочей нагрузкой. [Скачайте это приложение](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Для ASP.NET (не ядра), с помощью Visual Studio 2017 требуется .NET Framework 4.7.1 или более поздней версии средства разработки, которые не устанавливаются по умолчанию. Чтобы установить их, запустите Visual Studio Installer, щелкните **Изменить** и выберите **Отдельные компоненты**. Затем справа разверните элемент **ASP.NET и разработка веб-приложений** и выберите **Средства разработки для платформы .NET Framework 4.7.1**.
- ASP.NET 4.7.1 или более поздней версии, или открыть веб-проект ASP.NET Core 2.0.

## <a name="add-key-vault-support-to-your-project"></a>Добавление поддержки Key Vault в проект

1. В **обозревателе решений** выберите **Добавить** > **Подключенная служба**.
   Откроется страница "Подключенная служба" с перечнем служб, которые можно добавить в проект.
1. В меню доступных служб выберите **Безопасно храните секреты в Azure Key Vault**.

   ![Выбор пункта "Безопасно храните секреты в Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Если вы выполнили вход в Visual Studio и с вашей учетной записью связана подписка Azure, отобразится страница с раскрывающимся списком ваших подписок. Убедитесь, что вы вошли в Visual Studio и это та же учетная запись, которую вы используете для подписки Azure.

1. Выберите подписку, которую вы хотите использовать, а затем создайте новое или выберите существующее хранилище ключей. Можно также щелкнуть ссылку "Изменить", чтобы изменить автоматически созданное имя.

   ![Выберите свою подписку.](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Введите имя для хранилища ключей.

   ![Переименование хранилища ключей и выбор группы ресурсов](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Выберите существующую группу ресурсов, или создайте новый, указав уникальное имя, созданное автоматически.  Если вы хотите создать группу с другим именем, можно использовать [портал Azure](https://portal.azure.com). После этого закройте страницу и перезагрузите ее, чтобы обновить список групп ресурсов.
1. Выберите регион для создания хранилища ключей. Если ваше веб-приложение размещено в Azure, выберите регион, в котором размещается это веб-приложение, чтобы обеспечить оптимальную производительность.
1. Выберите ценовую модель. Дополнительные сведения см. в разделе [Цены на Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Нажмите кнопку "ОК", чтобы принять параметры конфигурации.
1. Щелкните **Добавить**, чтобы создать хранилище ключей. Процесс создания может завершиться ошибкой, если вы выбрали имя, которое уже используется.  В этом случае воспользуйтесь ссылкой **Изменить**, чтобы переименовать хранилище ключей, и повторите попытку.

   ![Добавление подключенной службы в проект](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Теперь добавьте секрет в хранилище ключей в Azure. Чтобы перейти на соответствующую страницу на портале, щелкните ссылку "Управлять секретами, хранящимися в этом Key Vault". Если вы закрыли страницу или проект, то можете перейти на эту страницу на [портале Azure](https://portal.azure.com). Для этого выберите **Все службы**, в разделе **Безопасность** выберите **Key Vault**, затем выберите созданное хранилище ключей.

   ![Переход на портал](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. В разделе "Key Vault" для созданного вами хранилища ключей выберите **Секреты**, затем выберите **Generate/Import** (Создать и импортировать).

   ![Создание и импорт секрета](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Введите секрет, например "MySecret", и присвойте ему любое строковое значение для проверки, затем нажмите кнопку **Создать**.

   ![Создание секрета](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (Необязательно.) Введите еще один секрет, но на этот раз поместите его в категорию, присвоив ему имя Secrets--MySecret. Эта синтаксическая конструкция задает категорию Secrets, которая содержит секрет MySecret.
 
Теперь можно обращаться к секретам в коде. Дальнейшие действия отличаются в зависимости от того, используется ASP.NET 4.7.1 или ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>Получите доступ к секретам в коде

1. Установите эти два пакета NuGet: [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) и [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Откройте файл Program.cs и замените существующий код следующим: 
   ```
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

3. Затем откройте файл About.cshtml.cs и написать следующий код:
   1. Включите ссылку Microsoft.Extensions.Configuration этим объектом, с помощью инструкции:

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

   1. Обновите метод OnGet. Обновите значение заполнителя, здесь показано имя секрета, созданный в указанные выше команды.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Запустите приложение локально, перейдя на страницу About. Вы должны увидеть извлечь значение секрета.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов, если она больше не нужна. При этому будут удалены хранилище ключей и связанные ресурсы. Чтобы удалить группу ресурсов на портале, сделайте следующее:

1. В поле поиска в верхней части портала введите имя группы ресурсов. Если в результатах поиска отображается группа ресурсов, используемая в этом кратком руководстве, выберите ее.
2. Выберите **Удалить группу ресурсов**.
3. В поле **Введите имя группы ресурсов:** введите имя группы ресурсов и выберите **Удалить**.

## <a name="how-your-aspnet-core-project-is-modified"></a>Как изменяется вашего проекта ASP.NET Core

В этом разделе описаны конкретные изменения, внесенные в проекте ASP.NET при добавлении Key Vault подключенной службы, с помощью Visual Studio.

### <a name="added-references"></a>Добавленные ссылки

Влияет на ссылки на .NET файлы проекта и ссылки на пакет NuGet.

| type | Справочные материалы |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files"></a>Добавленные файлы

- Добавлен файл ConnectedService.json, в который записываются некоторые сведения о поставщике подключенной службы, версия и ссылка на документацию.

### <a name="project-file-changes"></a>Изменения в файле проекта

- Добавлены элемент ItemGroup "Подключенные службы" и файл ConnectedServices.json.

### <a name="launchsettingsjson-changes"></a>Изменения в launchsettings.json

- В профиль IIS Express и профиль, который соответствует имени вашего веб-проекта, добавлены следующие записи переменных среды.

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure"></a>Изменения в Azure

- Создана группа ресурсов или использована существующая.
- В указанной группе ресурсов создано хранилище ключей.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Как изменяется проекта платформы ASP.NET

В этом разделе описаны конкретные изменения, внесенные в проекте ASP.NET при добавлении Key Vault подключенной службы, с помощью Visual Studio.

### <a name="added-references"></a>Добавленные ссылки

Влияет на ссылки на .NET файлы проекта и `packages.config` (ссылки NuGet).

| type | Справочные материалы |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files"></a>Добавленные файлы

- Добавлен файл ConnectedService.json, в который записываются некоторые сведения о поставщике подключенной службы, версия и ссылка на документацию.

### <a name="project-file-changes"></a>Изменения в файле проекта

- Добавлены элемент ItemGroup "Подключенные службы" и файл ConnectedServices.json.
- Ссылки на сборки .NET, описанные в разделе [Добавленные ссылки](#added-references).

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

### <a name="changes-on-azure"></a>Изменения в Azure

- Создана группа ресурсов или использована существующая.
- В указанной группе ресурсов создано хранилище ключей.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о разработке для Key Vault в [руководстве разработчика для Key Vault](key-vault-developers-guide.md).