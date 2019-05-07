---
title: Цикл обратной связи Персонализатора
titleSuffix: Azure Cognitive Services
description: Узнайте, как персонализировать содержимое с помощью службы "Персонализатор", из этого краткого руководства для C#.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: c566d1fd4b151efc0d28b7059504e60a1451c034
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025512"
---
# <a name="quickstart-personalize-content-using-c"></a>Краткое руководство. Персонализация содержимого с помощью C# 

Отобразите персонализированное содержимое с помощью службы "Персонализатор" благодаря этому краткому руководству для C#.

В этом примере показано, как использовать клиентскую библиотеку Персонализации для C# для выполнения следующих действий: 

 * составление списка действий для персонализации;
 * создание отчета о вознаграждении, которое будет выделено для действий с наивысшим рейтингом на основе выбора пользователя для указанного события.

Начало работы с Персонализацией включает в себя следующие шаги:

1. использование пакета SDK; 
1. написание кода для ранжирования действий, которые требуется отобразить пользователям;
1. написание кода для отправки вознаграждения для обучения цикла.

## <a name="prerequisites"></a>Предварительные требования

* Вам потребуется ключ подписки и URL-адрес службы выдачи токенов.
* [Visual Studio 2015 или 2017](https://visualstudio.microsoft.com/downloads/).
* Пакет SDK NuGet для Microsoft.Azure.CognitiveServices.Personalization. Инструкции по установке приведены ниже.

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Создание нового консольного приложения и использование пакета SDK Персонализатора 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Создайте консольное приложение Visual C# в Visual Studio.
1. Установите пакет NuGet клиентской библиотеки Персонализации. В меню выберите **Средства**, **Диспетчер пакетов NuGet**, а затем **Manage NuGet Packages for Solution** (Управление пакетами NuGet для решения).
1. Выберите вкладку **Обзор** и в поле **Поиск** введите `Microsoft.Azure.CognitiveServices.Personalization`.
1. Когда отобразится **Microsoft.Azure.CognitiveServices.Personalization**, выберите его.
1. Установите флажок рядом с названием своего проекта и выберите **Установить**

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Добавление кода и размещение ключей Персонализатора и Azure

1. Замените Program.cs следующим кодом. 
1. Замените значение `serviceKey` действительным ключом подписки Персонализатора.
1. Замените `serviceEndpoint` конечной точкой службы. Например, `https://westus2.api.cognitive.microsoft.com/`.
1. Запустите программу.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Добавление кода для ранжирования действий, которые требуется отобразить пользователям

Следующий код C# представляет собой полный список для передачи сведений о пользователе, _признаках и информации о вашем содержимом (_действиях_) в Персонализатор с помощью пакета SDK. Персонализатор возвращает высокоприоритетное действие, чтобы отобразить его пользователю.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalization;
using Microsoft.Azure.CognitiveServices.Personalization.Models;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PersonalizationExample
{
    class Program
    {
        // The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string serviceKey = "";

        // The endpoint specific to your personalization service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string serviceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            Uri url = new Uri(serviceEndpoint);

            // Get the actions list to choose from personalization with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalization client.
            PersonalizationClient client = InitializePersonalizationClient(url);

            do
            {
                Console.WriteLine("\nIteration: " + iteration++);

                // Get context information from the user.
                string timeOfDayFeature = GetUsersTimeOfDay();
                string tasteFeature = GetUsersTastePreference();

                // Create current context from user specified data.
                IList<object> currentContext = new List<object>() {
                    new { time = timeOfDayFeature },
                    new { taste = tasteFeature }
                };

                // Exclude an action for personalization ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalization service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

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

                Console.WriteLine("\nPersonalization service ranked the actions with the probabilities as below:");
                foreach (var rankedResponse in response.Ranking)
                {
                    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
                }

                // Send the reward for the action based on user response.
                client.Reward(response.EventId, new RewardRequest(reward));

                Console.WriteLine("\nPress q to break, any other key to continue:");
                runLoop = !(GetKey() == "Q");

            } while (runLoop);
        }

        /// <summary>
        /// Initializes the personalization client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalization client instance</returns>
        static PersonalizationClient InitializePersonalizationClient(Uri url)
        {
            PersonalizationClient client = new PersonalizationClient(url,
            new ApiKeyServiceClientCredentials(serviceKey),
            new DelegatingHandler[] { });

            return client;
        }

        /// <summary>
        /// Get users time of the day context.
        /// </summary>
        /// <returns>Time of day feature selected by the user.</returns>
        static string GetUsersTimeOfDay()
        {
            string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

            Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
            if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
                timeIndex = 1;
            }

            return timeOfDayFeatures[timeIndex - 1];
        }

        /// <summary>
        /// Gets user food preference.
        /// </summary>
        /// <returns>Food taste feature selected by the user.</returns>
        static string GetUsersTastePreference()
        {
            string[] tasteFeatures = new string[] { "salty", "sweet" };

            Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
            if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
                tasteIndex = 1;
            }

            return tasteFeatures[tasteIndex - 1];
        }

        /// <summary>
        /// Creates personalization actions feature list.
        /// </summary>
        /// <returns>List of actions for personalization.</returns>
        static IList<RankableAction> GetActions()
        {
            IList<RankableAction> actions = new List<RankableAction>
            {
                new RankableAction
                {
                    Id = "pasta",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
                },

                new RankableAction
                {
                    Id = "ice cream",
                    Features =
                    new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
                },

                new RankableAction
                {
                    Id = "juice",
                    Features =
                    new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
                },

                new RankableAction
                {
                    Id = "salad",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
                }
            };

            return actions;
        }

        private static string GetKey()
        {
            return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
        }
    }
}
```

## <a name="run-the-program"></a>Запуск программы

Выполните сборку и запуск программы. Программа быстрого запуска задает несколько вопросов, чтобы собрать настройки пользователя, известные как признаки, а затем предоставляет наиболее эффективные действия.

![Программа быстрого запуска задает несколько вопросов, чтобы собрать настройки пользователя, известные как признаки, а затем предоставляет наиболее эффективные действия.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Очистка ресурсов
Когда вы закончите работу с этим руководством, удалите все созданные при работе с ним файлы. 

## <a name="next-steps"></a>Дополнительная информация

[Принцип работы Персонализатора](how-personalizer-works.md)


