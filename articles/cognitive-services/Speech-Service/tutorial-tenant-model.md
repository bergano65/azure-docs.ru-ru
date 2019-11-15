---
title: Создание клиентской модели (предварительная версия) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Автоматически создайте клиентскую модель (службу "Пользовательское распознавание речи" с данными Office 365), которая использует данные из Office 365 для оптимального распознавания терминов, используемых в организации, а также безопасна и соответствует требованиям.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: c8a2855ce9cd320be3aea8b3b4a05f3b3eb39976
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73578218"
---
# <a name="create-a-tenant-model-preview"></a>Создание клиентской модели (предварительная версия)

Клиентская модель (служба "Пользовательское распознавание речи" с данными Office 365) предоставляется корпоративным клиентам Office 365 по запросу и позволяет автоматически создать пользовательскую модель распознавания речи на основе данных Office 365 в организации. Создаваемая модель будет оптимизирована для безопасного и соответствующего требованиям распознавания технических и жаргонных терминов, а также имен сотрудников, используемых в организации.

> [!IMPORTANT]
> Если ваша организация зарегистрируется для использования клиентской модели, служба распознавания речи сможет получать доступ к ее языковой модели, сформированной на основе документов и электронных писем из общедоступных групп Office 365, которые видны всем пользователям в организации. Администратор Office 365 вашей организации может включать и отключать использование языковой модели на уровне организации с помощью портала администрирования Office 365.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Регистрация для использования клиентской модели в центре администрирования Microsoft 365
> * Получение ключа подписки для службы "Речь"
> * Создание клиентской модели
> * Развертывание клиентской модели
> * Использование клиентской модели с пакетом SDK службы "Речь"

## <a name="enroll-using-the-microsoft-365-admin-center"></a>Регистрация с использованием центра администрирования Microsoft 365

Прежде чем развертывать клиентскую модель, необходимо пройти регистрацию с использованием центра администрирования Microsoft 365. Эту задачу может выполнять только администратор Microsoft 365 вашей организации.

1. Выполните вход в [центр администрирования Microsoft 365](https://admin.microsoft.com ).
2. На панели слева выберите **Параметры** и затем **Приложения**.

   ![Регистрация клиентской модели](media/tenant-language-model/tenant-language-model-enrollment.png)

3. Найдите и выберите **службы распознавания речи Azure**.

   ![Регистрация клиентской модели 2](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. Установите флажок и сохраните изменения.

Чтобы отключить клиентскую модель, вернитесь на этот экран, снимите соответствующий флажок и сохраните изменения.

## <a name="get-a-speech-subscription-key"></a>Получение ключа подписки для службы "Речь"

Чтобы использовать модель клиента с пакетом SDK службы "Речь", вам потребуется ресурс службы распознавания речи и связанный с ним ключ подписки.

1. Войдите на [портал Azure](https://aka.ms/azureportal).
2. Выберите **Создать ресурс**.
3. В строке поиска введите: **Речь**.
4. Выберите **Речь** и щелкните **Создать**.
5. Создайте ресурс, следуя выводимым на экран инструкциям. Проверьте следующее.
   * Параметр **Расположение** имеет значение **eastus** или **westus**.
   * Параметр **Ценовая категория** имеет значение **S0**.
6. Нажмите кнопку **Создать**.
7. Создание ресурса займет несколько минут. Ключ подписки будет представлен в разделе **Обзор** для вашего ресурса.

## <a name="create-a-model"></a>Создание модели

После того как администратор включит клиентскую модель для вашей организации, вы можете создать языковую модель на основе данных Office365.

1. Войдите в [Speech Studio](https://speech.microsoft.com/).
2. В правом верхнем углу щелкните значок шестеренки (параметры) и выберите **Параметры клиентской модели**.

   ![Меню параметров](media/tenant-language-model/tenant-language-settings.png)

3. На этом этапе появится сообщение о том, что вы можете создать клиентскую модель.
   > [!NOTE]
   > Создавать клиентскую модель на английском языке могут корпоративные клиенты Office 365 из Северной Америки. Если вы используете защищенное хранилище (CLB), ключ клиента (CK) или Office 365 для государственных организаций, эта возможность будет недоступна. Чтобы определить, используете ли вы защищенное хранилище клиента или ключ клиента, выполните следующие инструкции.
   > * [Защищенное хранилище клиента](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Ключ клиента](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)
   > * [Office 365 для государственных организаций](https://www.microsoft.com/microsoft-365/government)

4. Далее выберите **Согласиться**. После того как клиентская модель будет готова, вы получите сообщение электронной почты.

## <a name="deploy-your-model"></a>Развертывание модели

Чтобы развернуть готовую клиентскую модель, выполните следующие действия.

1. Нажмите кнопку **Просмотреть модель** в полученном сообщении электронной почты с подтверждением, либо выполните вход в [Speech Studio](https://speech.microsoft.com/).
2. В правом верхнем углу щелкните значок шестеренки (параметры) и выберите **Параметры клиентской модели**.

   ![Меню параметров](media/tenant-language-model/tenant-language-settings.png)

3. Щелкните **Развернуть**.
4. При развертывании состояние модели изменится на **Развернуто**.

## <a name="use-your-model-with-the-speech-sdk"></a>Использование модели с пакетом SDK службы "Речь"

После развертывания модели вы можете использовать ее с пакетом SDK службы "Речь". В этом разделе вы используете предоставленный образец кода для вызова службы "Речь" с использованием проверки подлинности Azure AD.

Рассмотрим код, используемый для вызова пакета SDK службы "Речь" в C#. В этом примере будет выполняться распознавание речи с использованием клиентской модели. В рамках этого руководства подразумевается, что настройка платформы уже была выполнена заранее. Если вам нужна помощь в настройке, ознакомьтесь со статьей [Краткое руководство. Распознавание речи с помощью C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

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

    // Note: ServiceApplicationId is a fixed value.  No need to change.

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

Далее необходимо повторно построить и запустить проект из командной строки. Перед выполнением команды необходимо обновить ряд параметров.

1. Вместо `<Username>` и `<Password>` укажите соответствующие значения для допустимого пользователя клиента.
2. Вместо `<Subscription-Key>` укажите ключ подписки для службы "Речь". Это значение представлено в разделе **Обзор** для вашего ресурса службы "Речь" на [портале Azure](https://aka.ms/azureportal).
3. Вместо `<Endpoint-Uri>` укажите приведенную ниже конечную точку. Вместо `{your-region}` необходимо указать регион, в котором был создан ваш ресурс службы "Речь". Поддерживаются следующие регионы: `westus`, `westus2` и `eastus`. Сведения о регионе представлены в разделе **Обзор** для вашего ресурса службы "Речь" на [портале Azure](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. Выполните приведенную ниже команду.
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>Дополнительная информация

* [Speech Studio](https://speech.microsoft.com/)
* [пакет SDK для службы "Речь"](speech-sdk.md);
