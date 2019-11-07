---
title: Краткое руководство. Клиентская библиотека Персонализатора для .NET | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Приступите к работе с клиентской библиотекой Персонализатора для .NET, используя цикл обучения.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: b86a8df86b7f9b8a5936752a5f0413aa863ae85f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490796"
---
# <a name="quickstart-personalizer-client-library-for-net"></a>Краткое руководство. Клиентская библиотека Персонализатора для .NET

Отобразите персонализированное содержимое с помощью службы "Персонализатор" благодаря этому краткому руководству для C#.

Приступите к работе с клиентской библиотекой Персонализатора для .NET. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.

 * составление списка действий для персонализации;
 * создание отчетов об оценке вознаграждения, указывающей на успешное выполнение действия с высшим рейтингом.

[Справочная документация](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [Примеры](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Использование этого краткого руководства

Для использования этого краткого руководства необходимо выполнить несколько действий.

* Создайте ресурс "Персонализатор" на портале Azure.
* На портале Azure в ресурсе "Персонализатор" на странице **Настройка** измените частоту обновления модели.
* В редакторе кода создайте и измените файл кода.
* В командной строке или терминале установите пакет SDK из командной строки.
* В командной строке (или терминале) запустите файл кода.

## <a name="create-a-personalizer-azure-resource"></a>Создание ресурса Azure для Персонализатора

Создайте ресурс для Персонализатора с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Также можно:

* получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services) на 7 дней. После регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки создайте две [переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_RESOURCE_KEY` — для ключа ресурса.
* `PERSONALIZER_RESOURCE_ENDPOINT` — для конечной точки ресурса.

На портале Azure ключ и значения конечных точек можно найти на странице **быстрый запуск**.

## <a name="change-the-model-update-frequency"></a>Изменение частоты обновления модели

На портале Azure в ресурсе "Персонализатор" на странице **Настройка** установите значение для параметра **Частота обновления модели** на 10 секунд. С такой высокой частотой служба будет обучаться быстро, и вы увидите, как главное действие меняется с каждой итерацией.

![Изменение частоты обновления модели](./media/settings/configure-model-update-frequency-settings.png)

После создания цикла Персонализатора модель не используется, поскольку отсутствуют вызовы API вознаграждения для обучения. Вызовы ранжирования будут возвращать равновероятностный результат для каждого элемента. Приложение должно по-прежнему ранжировать содержимое на основе выходных данных RewardActionId.

## <a name="create-a-new-c-application"></a>Создание нового приложения C#

Создайте консольное приложение .NET Core на C# в предпочитаемой интегрированной среде разработки или редакторе. 

В окне консоли (cmd, PowerShell или Bash) выполните команду dotnet `new`, чтобы создать консольное приложение с именем `personalizer-quickstart`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: `Program.cs`. 

```console
dotnet new console -n personalizer-quickstart
```

Измените каталог на созданную папку приложения. Чтобы создать приложение, выполните следующую команду:

```console
dotnet build
```

Выходные данные сборки не должны содержать предупреждений или ошибок. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="install-the-sdk"></a>Установка пакета SDK

В каталоге приложения установите клиентскую библиотеку Персонализатора для .NET с помощью следующей команды:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0
```

Если вы используете интегрированную среду разработки Visual Studio, клиентская библиотека доступна в виде загружаемого пакета NuGet.

## <a name="object-model"></a>Объектная модель

Клиент Персонализатора — это объект [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet), который проходит проверку подлинности в Azure с помощью объекта Microsoft.Rest.ServiceClientCredentials, который содержит ваш ключ.

Чтобы запросить ранжирование содержимого, создайте [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview), а затем передайте его в метод [client.Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview). Метод ранжирования возвращает RankResponse, содержащий ранжированное содержимое. 

Чтобы отправить вознаграждение в Персонализатор, создайте [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview), а затем передайте его в метод [client.Reward](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview). 

Определение вознаграждения в этом кратком руководстве тривиально. Со временем определение факторов влияния на [оценку вознаграждения](concept-rewards.md) и сложность процесса в рабочей системе может измениться. Это решение должно быть одним из основных решений в вашей архитектуре Персонализатора. 

## <a name="code-examples"></a>Примеры кода

Фрагменты кода по приведенным ниже ссылкам показывают, как выполнить следующие задачи с помощью клиентской библиотеки Персонализатора для .NET:

* [создание клиента Персонализатора](#create-a-personalizer-client);
* [запрос ранжирования](#request-a-rank);
* [отправка вознаграждения](#send-a-reward).

## <a name="add-the-dependencies"></a>Добавление зависимостей

В каталоге проекта откройте файл **Program.cs** в предпочитаемом редакторе или интегрированной среде разработки. Замените существующий код `using` следующими директивами `using`.

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Добавление сведений о ресурсе Персонализатора

В классе **Program** создайте переменные для ключа Azure и конечной точки вашего ресурса, извлеченных из переменных среды с именами `PERSONALIZER_RESOURCE_KEY` и `PERSONALIZER_RESOURCE_ENDPOINT`. Если вы создали переменные среды после запуска приложения, то для доступа к переменной следует закрыть и перезагрузить редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена. Методы будут созданы позже в этом руководстве.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Создание клиента Персонализатора

Теперь создайте метод для возврата клиента Персонализатора. Параметр метода — `PERSONALIZER_RESOURCE_ENDPOINT`, а apiKey — `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-content-choices-represented-as-actions"></a>Получение вариантов содержимого, представленных в виде действий

Действия представляют выборы содержимого, которые Персонализатор должен ранжировать. Добавьте следующие методы в класс Program, чтобы получить из командной строки пользовательские входные данные о времени суток и текущем предпочтении в пище.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Оба метода используют метод `GetKey` для чтения выбора пользователя из командной строки. 

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Создание цикла обучения

Цикл обучения персонализации — это цикл вызовов ранжирования и вознаграждений. В этом кратком руководстве за каждым вызовом ранжирования для персонализации содержимого следует вызов вознаграждения. Это необходимо, чтобы определить, насколько хорошо служба оценивает содержимое. 

Следующий код в методе `main` программы циклически запрашивает у пользователя предпочтения в командной строке, отправляя эти сведения в Персонализатор для ранжирования. Затем он предоставляет список вариантов клиенту для выбора и отправляет вознаграждения в Персонализатор, указывая, насколько хорошо служба выполнила ранжирование выбора.

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Initialize Personalizer client.
    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        // <rank>
        // Get context information from the user.
        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        // Create current context from user specified data.
        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        // Exclude an action for personalizer ranking. This action will be held at its current position.
        // This simulates a business rule to force the action "juice" to be ignored in the ranking.
        // As juice is excluded, the return of the API will always be with a probability of 0.
        IList<string> excludeActions = new List<string> { "juice" };

        // Generate an ID to associate with the request.
        string eventId = Guid.NewGuid().ToString();

        // Rank the actions
        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);
        // </rank>

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        // <reward>
        float reward = 0.0f;
        string answer = GetKey();

        if (answer == "Y")
        {
            reward = 1;
            Console.WriteLine("\nGreat! Enjoy your food.");
        }
        else if (answer == "N")
        {
            reward = 0;
            Console.WriteLine("\nYou didn't like the recommended food choice.");
        }
        else
        {
            Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
        }

        Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
        foreach (var rankedResponse in response.Ranking)
        {
            Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
        }

        // Send the reward for the action based on user response.
        client.Reward(response.EventId, new RewardRequest(reward));
        // </reward>

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

Перед запуском файла кода добавьте следующие методы для [получения вариантов содержимого](#get-content-choices-represented-as-actions):

* GetUsersTimeOfDay
* GetUsersTastePreference
* GetKey

## <a name="request-a-rank"></a>Запрос ранжирования

Чтобы выполнить запрос ранжирования, программа запрашивает предпочтения пользователя для создания `currentContent` вариантов содержимого. Процесс может создавать содержимое для исключения из рейтинга. Это содержимое отображается в виде `excludeActions`. Запрос ранжирования требует действий, currentContext, excludeActions и уникального идентификатора события ранжирования (такого как GUID), чтобы получить ранжированный ответ. 

В этом кратком руководстве используются простые контекстные признаки времени суток и предпочтений пользователя в пище. В рабочих системах определение и [оценка](concept-feature-evaluation.md) [действий и функций](concepts-features.md) может отличаться.  

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Отправка вознаграждения

Чтобы выполнить запрос вознаграждения, программа получает выбор пользователя из командной строки, присваивает числовое значение каждому выбору, а затем отправляет уникальный идентификатор события ранжирования и числовое значение в метод вознаграждения.

В этом кратком руководстве в качестве вознаграждения используется простое число: ноль или 1. В рабочих системах определение времени и содержимого ответа на вызов [вознаграждения](concept-rewards.md) может быть нетривиальным. Это зависит от конкретных потребностей. 

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Запуск программы

Перейдите к каталогу приложения и запустите приложение с помощью команды dotnet `run`.

```console
dotnet run
```

![Программа быстрого запуска задает несколько вопросов, чтобы собрать настройки пользователя, известные как признаки, а затем предоставляет наиболее эффективные действия.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[Исходный код для этого краткого руководства](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) доступен в примерах для Персонализатора в репозитории GitHub.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
>[Принцип работы Персонализатора](how-personalizer-works.md)

* [Что такое служба "Персонализатор"?](what-is-personalizer.md)
* [Где можно использовать Персонализатор](where-can-you-use-personalizer.md)
* [Устранение неполадок](troubleshooting.md)

