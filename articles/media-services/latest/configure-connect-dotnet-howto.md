---
title: Подключение к API v3 служб мультимедиа Azure — .NET
description: Узнайте, как подключиться к API служб мультимедиа версии 3 с помощью .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: juliako
ms.openlocfilehash: a256eb787d7e3dbd800ec2e630cac591b07ca0fc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444169"
---
# <a name="connect-to-media-services-v3-api---net"></a>Подключение к API v3 служб мультимедиа - .NET

В этой статье показано, как соединиться с пакета SDK для .NET v3 для служб мультимедиа Azure с помощью метода под основной учетной записью службы.

## <a name="prerequisites"></a>Технические условия

- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Запомните имя группы ресурсов и имя учетной записи служб мультимедиа
- Установите это средство, которое вы хотите использовать для разработки приложений .NET. В этой статье показано, как использовать [2019 г. Visual Studio Community Edition](https://www.visualstudio.com/downloads/). Вы можете использовать Visual Studio Code, см. в разделе [работа с C# ](https://code.visualstudio.com/docs/languages/csharp). Или можно использовать другом редакторе кода.

## <a name="create-a-console-application"></a>Создание консольного приложение

1. Запустите Visual Studio. 
1. Из **файл** меню, щелкните **New** > **проекта**. 
1. Создание **.NET Core** консольное приложение.

Предназначен для примера приложения в этом разделе `netcoreapp2.0`. Код использует «асинхронным методом main», который доступен, начиная с C# 7.1. См. в разделе, это [блог](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) для получения дополнительных сведений.

## <a name="add-required-nuget-packages"></a>Добавьте необходимые пакеты NuGet

1. В Visual Studio выберите **средства** > **диспетчер пакетов NuGet** > **консоли диспетчера NuGet**.
2. В **консоль диспетчера пакетов** окно, используйте `Install-Package` команду, чтобы добавить следующие пакеты NuGet. Например, `Install-Package Microsoft.Azure.Management.Media`.

|Пакет|Описание|
|---|---|
|`Microsoft.Azure.Management.Media`|Пакет SDK служб мультимедиа Azure. <br/>Чтобы убедиться, что вы используете последнюю версию пакета служб мультимедиа Azure, проверьте [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Библиотека проверки подлинности ADAL для пакета Azure SDK для NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Чтение значения конфигурации из переменных среды и локальные файлы JSON|
|`Microsoft.Extensions.Configuration.Json`|Чтение значения конфигурации из переменных среды и локальные файлы JSON
|`WindowsAzure.Storage`|Пакет SDK для хранилища|

## <a name="create-and-configure-the-app-settings-file"></a>Создание и настройка файла параметров приложения

### <a name="create-appsettingsjson"></a>Создание appsettings.json

1. Последовательно выберите go **Общие** > **текстовый файл**.
1. Назовите его «appsettings.json».
1. Задайте для свойства «Копировать в выходной каталог» значение «Копировать более позднюю версию» JSON-файл (так что приложение может получить доступ к его при публикации).

### <a name="set-values-in-appsettingsjson"></a>Установленные значения в файле appsettings.json

Запустите `az ams account sp create` команды, как описано в разделе [доступа к интерфейсам API](access-api-cli-how-to.md). Эта команда возвращает json, который следует копировать в «appsettings.json».
 
## <a name="add-configuration-file"></a>Добавление файла конфигурации

Для удобства добавьте файл конфигурации, который отвечает за чтение значения из «appsettings.json».

1. Добавьте новый класс .cs в проект. Назовите его `ConfigWrapper`. 
1. Вставьте следующий код в этом файле (в этом примере предполагается, что у вас есть пространство имен является `ConsoleApp1`).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Подключиться к клиенту .NET

Чтобы начать использование API Служб мультимедиа с .NET, создайте объект **AzureMediaServicesClient**. Чтобы создать объект, введите учетные данные, необходимые клиенту для подключения к Azure с помощью Azure AD. В приведенном ниже коде функция GetCredentialsAsync создает объект ServiceClientCredentials, исходя из учетных данных, указанное в локальный файл конфигурации.

1. Откройте `Program.cs`.
1. Вставьте следующий код:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

- [Учебник. Отправка, кодирование и потоковая передача видео с помощью .NET](stream-files-tutorial-with-api.md) 
- [Учебник. Потоковая трансляция в реальном времени с помощью Служб мультимедиа Azure версии 3 и .NET](stream-live-tutorial-with-api.md)
- [Учебник. Анализ видео с помощью Служб мультимедиа версии 3 и .NET](analyze-videos-tutorial-with-api.md)
- [Создание входных данных задания из локального файла с помощью .NET](job-input-from-local-file-how-to.md)
- [Создание входных данных задания из URL-адреса HTTPS с помощью .NET](job-input-from-http-how-to.md)
- [Кодирование с помощью пользовательского преобразования с помощью .NET](customize-encoder-presets-how-to.md)
- [Использование динамического шифрования AES-128 и службы доставки ключей с помощью .NET](protect-with-aes128.md)
- [Использование динамического шифрования DRM и службы доставки лицензий с помощью .NET](protect-with-drm.md)
- [Получение ключа подписи из имеющейся политики с помощью .NET](get-content-key-policy-dotnet-howto.md)
- [Создание фильтров с помощью Служб мультимедиа для .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Дополнительные примеры видео по запросу с использованием Функций Azure версии 2 и Cлужб мультимедиа версии 3](https://aka.ms/ams3functions)

## <a name="see-also"></a>См. также

[Справочник по .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
