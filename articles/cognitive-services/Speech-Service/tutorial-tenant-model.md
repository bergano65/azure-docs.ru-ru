---
title: Создание клиентской модели (предварительная версия) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Автоматическое создание защищенной и соответствующей требованиям клиентскую модель (служба "Пользовательское распознавание речи" с данными из Office 365), которая использует данные из Office 365 для оптимального распознавания используемых в организации терминов.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 4fec6b93ad206ae3052df5f7763f3c146b7aa680
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446804"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Руководство. Создание клиентской модели (предварительная версия)

Клиентская модель (служба "Пользовательское распознавание речи" с данными из Office 365) предоставляется корпоративным клиентам Office 365 по запросу и позволяет автоматически создать пользовательскую модель распознавания речи на основе данных Office 365 в организации. Эта модель будет оптимизирована для безопасного и соответствующего требованиям распознавания технических и жаргонных терминов, а также имен сотрудников, используемых в организации.

> [!IMPORTANT]
> Если ваша организация регистрируется через службу клиентской модели, служба "Речь" получает доступ к языковой модели этой организации. Эта модель формируется на основе сообщений электронной почты и документов Office 365, доступных для всех пользователей в организации. Администратор Office 365 вашей организации может включать и отключать использование языковой модели на уровне организации через портал администрирования Office 365.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Регистрация в клиентской модели через центр администрирования Microsoft 365
> * Получение ключа подписки для службы "Речь"
> * Создание клиентской модели
> * Развертывание клиентской модели
> * Использование клиентской модели с пакетом SDK службы "Речь"

## <a name="enroll-in-the-tenant-model-service"></a>Регистрация в службе клиентской модели

Прежде чем развертывать клиентскую модель, необходимо зарегистрироваться в службе клиентской модели. Эта регистрация выполняется в центре администрирования Microsoft 365, и только администратором Microsoft 365.

1. Выполните вход в [центр администрирования Microsoft 365](https://admin.microsoft.com).

1. На панели слева выберите **Параметры**, затем **Приложения** и **Службы речи Azure**.

   ![Панель "Службы и надстройки"](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Установите флажок **Разрешить языковую модель уровня организации** и щелкните **Сохранить изменения**. 

   ![Панель служб речи Azure](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Чтобы отключить экземпляр модели клиента, выполните следующие действия.
1. Повторите шаги 1 и 2, описанные выше.
1. Снимите флажок **Разрешить языковую модель уровня организации** и щелкните **Сохранить изменения**.

## <a name="get-a-speech-subscription-key"></a>Получение ключа подписки для службы "Речь"

Чтобы использовать модель клиента с пакетом SDK службы "Речь", вам потребуется ресурс службы распознавания речи и связанный с ним ключ подписки.

1. Войдите на [портал Azure](https://aka.ms/azureportal).
1. Выберите **Создать ресурс**.
1. В поле **Поиск** введите **Речь**.
1. Выберите в списке результатов **Речь** и щелкните **Создать**.
1. Создайте ресурс, следуя выводимым на экран инструкциям. Убедитесь, что выполнены следующие условия:
   * Параметр **Расположение** имеет значение **eastus** или **westus**.
   * Параметр **Ценовая категория** имеет значение **S0**.
1. Нажмите кнопку **Создать**.

   Создание ресурса займет несколько минут. Ключ подписки будет представлен в разделе **Обзор** для вашего ресурса.

## <a name="create-a-language-model"></a>Создание языковой модели

После того как администратор включит клиентскую модель для вашей организации, вы сможете создать языковую модель на основе данных из Office365.

1. Войдите в [Speech Studio](https://speech.microsoft.com/).
1. В правом верхнем углу щелкните **Параметры** (значок шестеренки), а затем **Tenant Model settings** (Параметры клиентской модели).

   ![Ссылка на параметры клиентской модели](media/tenant-language-model/tenant-language-settings.png)

   Speech Studio отобразит сообщение о том, есть ли у вас права на создание клиентской модели.

   > [!NOTE]
   > Создавать клиентскую модель на английском языке могут корпоративные клиенты Office 365 из Северной Америки. Если вы используете защищенное хранилище, ключ клиента или Office 365 для государственных организаций, эта возможность будет недоступна. Чтобы определить, используете ли вы защищенное хранилище клиента или ключ клиента, воспользуйтесь следующей информацией.
   > * [Защищенное хранилище клиента](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Ключ клиента](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)
   > * [Office 365 для государственных организаций](https://www.microsoft.com/microsoft-365/government)

1. Выберите **Согласиться**. 

   Когда клиентская модель будет готова, вы получите сообщение электронной почты с подтверждением и дальнейшими инструкциями.

## <a name="deploy-your-tenant-model"></a>Развертывание клиентской модели

Когда экземпляр клиентской модели будет готов, разверните его следующим образом.

1. В подтверждающем сообщении электронной почты нажмите кнопку **View model** (Просмотреть модель). Или войдите в [Speech Studio](https://speech.microsoft.com/).
1. В правом верхнем углу щелкните **Параметры** (значок шестеренки), а затем **Tenant Model settings** (Параметры клиентской модели).

   ![Ссылка на параметры клиентской модели](media/tenant-language-model/tenant-language-settings.png)

1. Выберите **Развернуть**.

   После развертывания модели ее состояние принимает значение *Развернуто*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Использование клиентской модели с пакетом SDK службы "Речь"

После развертывания модели вы можете использовать ее с пакетом SDK службы "Речь". В этом разделе вы примените пример кода для вызова службы "Речь" с использованием проверки подлинности Azure Active Directory (AAD).

Рассмотрим код, используемый для вызова пакета SDK службы "Речь" в C#. В этом примере вы выполните распознавание речи с использованием клиентской модели. В рамках этого руководства подразумевается, что настройка платформы уже была выполнена заранее. Если вам нужна помощь в настройке, изучите статью [Краткое руководство. Распознавание речи с помощью C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

Скопируйте в проект следующий код:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Далее нужно заново скомпилировать и запустить проект из командной строки. Перед выполнением команды обновите несколько параметров следующим образом.

1. Вместо `<Username>` и `<Password>` укажите соответствующие значения для допустимого пользователя клиента.
1. Вместо `<Subscription-Key>` укажите ключ подписки для службы "Речь". Это значение представлено в разделе **Обзор** для вашего ресурса службы "Речь" на [портале Azure](https://aka.ms/azureportal).
1. Замените `<Endpoint-Uri>` приведенной ниже конечной точкой. Вместо `{your region}` необходимо указать регион, в котором был создан ваш ресурс службы "Речь". Поддерживаются следующие регионы: `westus`, `westus2` и `eastus`. Сведения о регионе представлены в разделе **Обзор** для ресурса службы "Речь" на [портале Azure](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Выполните следующую команду:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

В этом учебнике вы узнали, как использовать данные Office 365 для создания собственной модели распознавания речи, ее развертывания и использования с пакетом SDK Речь.

## <a name="next-steps"></a>Дальнейшие действия

* [Speech Studio](https://speech.microsoft.com/)
* [пакет SDK для службы "Речь"](speech-sdk.md);
