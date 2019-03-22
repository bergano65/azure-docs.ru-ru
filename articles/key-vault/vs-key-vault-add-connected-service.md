---
title: Добавление поддержки Key Vault в проект ASP.NET с помощью Visual Studio — Azure Key Vault | Документация Майкрософт
description: Это руководство поможет вам узнать, как добавить поддержку Key Vault в веб-приложение ASP.NET или ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: ghogen
ms.openlocfilehash: de849ae290228826ee500ae1c7e623210e585d34
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113254"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Добавление хранилища ключей в веб-приложение с помощью функции "Подключенные службы" в Visual Studio

В этом руководстве вы узнаете, как легко добавить все, что нужно, чтобы приступить к использованию Azure Key Vault для управления секретами веб-проектов в Visual Studio при работе с проектом ASP.NET Core или проектом ASP.NET любого типа. С помощью функции "Подключенные службы" в Visual Studio 2017 можно использовать Visual Studio, чтобы автоматически добавлять пакеты NuGet и параметры конфигурации, необходимые для подключения к хранилищу ключей в Azure. 

Подробные сведения об изменениях, вносимых функцией "Подключенные службы" в проект для поддержки Key Vault, можно получить из раздела [Что произошло с моим проектом ASP.NET при добавлении подключенной службы Key Vault в Visual Studio?](vs-key-vault-aspnet-what-happened.md) или [Что произошло с моим проектом ASP.NET Core при добавлении подключенной службы Key Vault в Visual Studio?](vs-key-vault-aspnet-core-what-happened.md)

## <a name="prerequisites"></a>Технические условия

- **Подписка Azure**. Если у вас нет подписки, вы можете зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 версии 15.7** с установленной рабочей нагрузкой **Веб-разработка**. [Скачайте это приложение](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Для ASP.NET (не Core) требуются средства разработки для платформы .NET Framework 4.7.1, которые не устанавливается по умолчанию. Чтобы установить их, запустите Visual Studio Installer, щелкните **Изменить** и выберите **Отдельные компоненты**. Затем справа разверните элемент **ASP.NET и разработка веб-приложений** и выберите **Средства разработки для платформы .NET Framework 4.7.1**.
- Откроется веб-проект ASP.NET 4.7.1 или ASP.NET Core 2.0.

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
3. Затем откройте файл About.cshtml.cs и напишите следующий код
   1. Включите ссылку Microsoft.Extensions.Configuration с помощью инструкции    
       ```
       using Microsoft.Extensions.Configuration
       ```
   2. Добавьте этот конструктор
       ```
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```
   3. Обновите метод OnGet. Обновите значение заполнителя, показанного здесь, а также имя секрета, созданное в указанных выше командах
       ```
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Запустите приложение локально, перейдя на страницу "О программе". Вы должны получить значение секрета

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов, если она больше не нужна. При этому будут удалены хранилище ключей и связанные ресурсы. Чтобы удалить группу ресурсов на портале, сделайте следующее:

1. В поле поиска в верхней части портала введите имя группы ресурсов. Если в результатах поиска отображается группа ресурсов, используемая в этом кратком руководстве, выберите ее.
2. Выберите **Удалить группу ресурсов**.
3. В поле **Введите имя группы ресурсов:** введите имя группы ресурсов и выберите **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о разработке для Key Vault в [руководстве разработчика для Key Vault](key-vault-developers-guide.md).