---
title: Подключение к Azure Media Services v3 API - .NET
description: В этой статье показано, как подключиться к Media Services v3 API с .NET.
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
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: b8f4de1a5b9d8216ae2442631f5f9135c3c72d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269813"
---
# <a name="connect-to-media-services-v3-api---net"></a>Подключение к Медиа-сервисам v3 API - .NET

В этой статье показано, как подключиться к службе мультимедиа Azure media Services v3 .NET SDK с помощью основного метода входа в службу.

## <a name="prerequisites"></a>Предварительные требования

- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Убедитесь в том, чтобы запомнить имя группы ресурсов и имя учетной записи Media Services
- Установите инструмент, который вы хотели бы использовать для разработки .NET. Шаги в этой статье показывают, как использовать [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Вы можете использовать визуальный код студии, [см. Работа с C .](https://code.visualstudio.com/docs/languages/csharp) Или можно использовать другой редактор кода.

> [!IMPORTANT]
> Просмотр [именования конвенций](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Создание консольного приложение

1. Запустите среду Visual Studio. 
1. Из меню **файла** нажмите **Новый** > **проект**. 
1. Создайте консольное приложение **.NET Core.**

Пример приложения в этой `netcoreapp2.0`теме, цели . В коде используется «основной асин», который доступен, начиная с 7.1. Смотрите этот [блог](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) для более подробной информации.

## <a name="add-required-nuget-packages"></a>Добавьте необходимые пакеты NuGet

1. В Visual Studio выберите набор**менеджеров** > по пакету **Инструментов** > **NuGet.**
2. В окне **консоли менеджера пакетов** используйте `Install-Package` команду для добавления следующих пакетов NuGet. Например, `Install-Package Microsoft.Azure.Management.Media`.

|Пакет|Описание|
|---|---|
|`Microsoft.Azure.Management.Media`|Служба мультимедиа Azure SDK. <br/>Чтобы убедиться, что вы используете последний пакет медиасервисов Azure, проверьте [Microsoft.Azure.Management.Media.](https://www.nuget.org/packages/Microsoft.Azure.Management.Media)|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Библиотека аутентификации ADAL для Azure SDK для NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Прочитайте значения конфигурации по переменным среды и локальным файлам JSON|
|`Microsoft.Extensions.Configuration.Json`|Прочитайте значения конфигурации по переменным среды и локальным файлам JSON
|`WindowsAzure.Storage`|Хранение SDK|

## <a name="create-and-configure-the-app-settings-file"></a>Создание и настройка файла настроек приложения

### <a name="create-appsettingsjson"></a>Создание appsettings.json

1. Перейти **Общий текстовый** > **файл**.
1. Назовите его "appsettings.json".
1. Установите свойство "Копировать в каталог вывода" файла .json на "Копировать, если новее" (чтобы приложение могло получить к нему доступ при публикации).

### <a name="set-values-in-appsettingsjson"></a>Установка значений в appsettings.json

Выполнить `az ams account sp create` команду, описанную в [AIS доступа.](access-api-cli-how-to.md) Команда возвращает json, который вы должны скопировать в ваш "appsettings.json".
 
## <a name="add-configuration-file"></a>Добавление файла конфигурации

Для удобства добавьте файл конфигурации, который отвечает за значение чтения из "appsettings.json".

1. Добавьте новый класс .cs в свой проект. Назовите его `ConfigWrapper`. 
1. Вставьте следующий код в этом файле (этот пример `ConsoleApp1`предполагает, что у вас есть пространство имен).

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

## <a name="connect-to-the-net-client"></a>Подключение к клиенту .NET

Чтобы начать использование API Служб мультимедиа с .NET, создайте объект **AzureMediaServicesClient**. Чтобы создать объект, введите учетные данные, необходимые клиенту для подключения к Azure с помощью Azure AD. В приведенном ниже коде функция GetCredentialsAsync создает объект ServiceClientCredentials на основе учетных данных, поставляемых в файле локальной конфигурации.

1. Откройте среду `Program.cs`.
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

- [Учебник: Загрузка, кодирование и потоковое видео - .NET](stream-files-tutorial-with-api.md) 
- [Учебник: Поток жить с Медиа Услуги v3 - .NET](stream-live-tutorial-with-api.md)
- [Учебник: Анализ видео с Медиа-сервисов v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Создание входных данных задания из локального файла с помощью .NET](job-input-from-local-file-how-to.md)
- [Создание входных данных задания из URL-адреса HTTPS с помощью .NET](job-input-from-http-how-to.md)
- [Кодирование с помощью пользовательского преобразования с помощью .NET](customize-encoder-presets-how-to.md)
- [Использование динамического шифрования AES-128 и службы доставки ключей с помощью .NET](protect-with-aes128.md)
- [Использование динамического шифрования DRM и службы доставки лицензий с помощью .NET](protect-with-drm.md)
- [Получение ключа подписи из имеющейся политики с помощью .NET](get-content-key-policy-dotnet-howto.md)
- [Создание фильтров с помощью Служб мультимедиа для .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Дополнительные примеры видео по запросу с использованием Функций Azure версии 2 и Cлужб мультимедиа версии 3](https://aka.ms/ams3functions)

## <a name="see-also"></a>См. также

* [Справочник по .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* Для получения дополнительных примеров кода [см.](https://github.com/Azure-Samples/media-services-v3-dotnet)
