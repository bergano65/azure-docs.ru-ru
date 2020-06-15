---
title: Использование веб-приложения — Персонализация
description: Настройка веб-приложения C# .NET с помощью цикла персонализации для предоставления правильного содержимого пользователю в зависимости от действий (с функциями) и функций контекста.
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.author: diberry
ms.openlocfilehash: 9def69a1540e81b99723c16ad34ba522d1737c7f
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84713920"
---
# <a name="add-personalizer-to-a-net-web-app"></a>Добавление персонализации в веб-приложение .NET

Настройка веб-приложения C# .NET с помощью цикла персонализации для предоставления правильного содержимого пользователю в зависимости от действий (с функциями) и функций контекста.

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Настройка ключа и конечной точки персонализации
> * Получение компонентов
> * Вызов API ранжирования и вознаграждений
> * Отобразить наиболее частное действие, обозначенное как _ревардактионид_



## <a name="select-the-best-content-for-a-web-app"></a>Выбор лучшего содержимого для веб-приложения

Веб-приложение должно использовать персонализацию при наличии на веб-странице списка _действий_ (определенного типа содержимого), которые должны быть персонализированы в один элемент (ревардактионид) для показа. Примеры списков действий включают новости, расположения для кнопок и варианты выбора слов для названий продуктов.

Вы отправляете список действий вместе с функциями контекста в цикл персонализации. Персонализация выбирает одно лучшее действие, а веб-приложение отображает это действие.

В этом руководстве действия относятся к типам продуктов питания:

* паста
* Мороженое
* сок
* салад
* попкорна
* кофе
* суп

Чтобы помочь программе персонализации узнать о своих действиях, отправьте оба действия (_)_с функциями_ и _контекстами_ при каждом запросе API ранжирования.

**Компонент** модели — это сведения о действии или контексте, которые могут быть объединены (сгруппированы) между членами базы пользователей веб-приложения. Функция _не_ зависит отдельно (например, идентификатор пользователя) или очень специфично (например, точное время суток).

### <a name="actions-with-features"></a>Действия с компонентами

Каждое действие (элемент содержимого) имеет функции, помогающие отличать элемент питания.

Функции не настраиваются как часть конфигурации цикла в портал Azure. Вместо этого они отправляются в виде объекта JSON с каждым вызовом API ранжирования. Это обеспечивает гибкость действий и их функций для увеличения, изменения и сжатия с течением времени, что позволяет персонализации отслеживать тенденции.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
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
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>Признаки контекста

Функции контекста помогают персонализации понять контекст действий. Контекст для этого примера приложения включает:

* время суток, утро, вечером, вечер, ночь
* предпочтения пользователя для «вкусов», «очень приятия», «печальном», «чнику» или «savory»
* Обозреватель контекста — агент пользователя, географическое расположение, источник ссылки

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Как веб-приложение использует персонализацию?

Веб-приложение использует персонализацию, чтобы выбрать лучшее действие из списка вариантов продуктов. Для этого в каждом вызове API ранжирования отправляется следующая информация:
* **действия** с их функциями, такими как `taste` и`spiceLevel`
* функции **контекста** , такие как `time` день, предпочтения пользователя `taste` и сведения о агенте пользователя браузера, а также функции контекста
* **действия, которые необходимо исключить** , например сок
* Идентификатор **EventID**, который отличается для каждого вызова API ранжирования.

## <a name="personalizer-model-features-in-a-web-app"></a>Функции модели персонализации в веб-приложении

Персонализация требует функций для действий (содержимого) и текущего контекста (пользователя и среды). Функции используются для выровняйте действия в текущем контексте модели. Модель представляет собой представление знаний персонализации о действиях, контексте и их функциях, позволяющих принимать обоснованные решения.

Модель, включая функции, обновляется по расписанию на основе параметра **частоты обновления модели** в портал Azure.

> [!CAUTION]
> Функции в этом приложении предназначены для иллюстрации функций и значений функций, но не обязательно для использования в веб-приложении.

### <a name="plan-for-features-and-their-values"></a>Планирование функций и их значений

Необходимо выбрать компоненты с тем же планированием и конструкцией, которые будут применяться к любой схеме или модели в вашей технической архитектуре. Значения компонентов можно задать с помощью бизнес-логики или сторонних систем. Значения компонентов не должны быть настолько строго специфичными, что они не применяются к группе или классу функций.

### <a name="generalize-feature-values"></a>Значения функций generalize

#### <a name="generalize-into-categories"></a>Обобщить по категориям

Это приложение использует `time` в качестве функции, но группирует время по категориям `morning` , таким как,, `afternoon` `evening` и `night` . Это пример использования сведений о времени, но не в особых случаях, таких как `10:05:01 UTC+2` .

#### <a name="generalize-into-parts"></a>Обобщить в части

Это приложение использует функции HTTP-запросов из браузера. Это начинается с очень определенной строки со всеми данными, например:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

Библиотека классов **хттпрекуестфеатурес** обобщает эту строку в объект **усеражентинфо** с отдельными значениями. Все значения, которые являются слишком специфичными, задаются пустой строкой. При отправке функций контекста для запроса он имеет следующий формат JSON:

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>Использование примера веб-приложения

Для запуска приложения в качестве примера веб-приложения на основе браузера (предоставляется весь код) должны быть установлены следующие приложения.

Установите следующее программное обеспечение:

* [.NET core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.1) — образец серверной части использует .NET Core
* [Node.js](https://nodejs.org/) -клиент/внешний интерфейс зависит от этого приложения
* [Visual studio 2019](https://visualstudio.microsoft.com/vs/)или [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/) — используйте среду разработчика Visual Studio 2019 или .NET Core CLI для сборки и запуска приложения.

### <a name="set-up-the-sample"></a>Настройка образца
1. Клонирование репозитория примеров персонализации Azure.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Перейдите в раздел _Samples/хттпрекуестфеатурес_ , чтобы открыть решение `HttpRequestFeaturesExample.sln` .

    При запросе разрешите Visual Studio обновить пакет .NET для персонализации.

### <a name="set-up-azure-personalizer-service"></a>Настройка службы "Персонализация Azure"

1. [Создайте ресурс персонализации](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) в портал Azure.

1. В портал Azure найдите параметр и либо `Endpoint` `Key1` `Key2` (либо будет работать) на вкладке **ключи и конечные точки** . Это ваши `PersonalizerServiceEndpoint` и ваши `PersonalizerApiKey` .
1. Заполните `PersonalizerServiceEndpoint` в **appsettings.json**.
1. Настройте в `PersonalizerApiKey` качестве [секрета приложения](https://docs.microsoft.com/aspnet/core/security/app-secrets) один из следующих способов.

    * Если вы используете .NET Core CLI, можно использовать `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` команду.
    * Если вы используете Visual Studio, можно щелкнуть правой кнопкой мыши проект и выбрать пункт " **Управление секретными данными пользователя** ", чтобы настроить ключи персонализации. При этом Visual Studio откроет файл, в `secrets.json` котором можно добавить ключи следующим образом:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>Запуск примера

Создайте и запустите Хттпрекуестфеатуресексампле с помощью одного из следующих методов:

* Visual Studio 2019: нажмите клавишу **F5**
* .NET Core CLI. `dotnet build` затем`dotnet run`

С помощью веб-браузера можно отправить запрос на ранжирование и запрос на вознаграждение, просмотреть ответы, а также функции HTTP-запросов, извлеченные из вашей среды.

> [!div class="mx-imgBorder"]
> ![Создайте и запустите проект Хттпрекуестфеатуресексампле. Откроется окно браузера для вывода одностраничного приложения.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Демонстрация цикла персонализации

1. Нажмите кнопку **сформировать новый запрос на ранжирование** , чтобы создать новый объект JSON для вызова API ранжирования. При этом создаются действия (с функциями) и функции контекста, а также отображаются значения, чтобы можно было увидеть, как выглядит JSON.

    Для собственного будущего приложения создание действий и функций может выполняться на клиенте, на сервере, на разных сочетаниях или с вызовами других служб.

1. Выберите **Отправить запрос на ранжирование** , чтобы отправить объект JSON на сервер. Сервер вызывает API ранжирования персонализации. Сервер получает ответ и возвращает отображаемое действие с наибольшим рангом клиенту.

1. Задайте значение вознаграждение, а затем нажмите кнопку **Отправить запрос на вознаграждение** . Если значение вознаграждение не изменено, клиентское приложение всегда отправляет значение `1` в персонализацию.

    > [!div class="mx-imgBorder"]
    > ![Создайте и запустите проект Хттпрекуестфеатуресексампле. Откроется окно браузера для вывода одностраничного приложения.](./media/tutorial-web-app/reward-score-api-call.png)

    Для вашего будущего приложения генерация оценки вознаграждений может произойти после сбора сведений о поведении пользователя на клиенте, а также бизнес-логики на сервере.

## <a name="understand-the-sample-web-app"></a>Общие сведения о примере веб-приложения

Пример веб-приложения содержит сервер **C# .NET** , который управляет сбором компонентов и отправкой и получением вызовов HTTP к конечной точке персонализации.

Пример веб-приложения использует **интерфейсное клиентское приложение маскирования** для захвата функций и обработки действий пользовательского интерфейса, таких как нажатие кнопок и отправка данных на сервер .NET.

В следующих разделах объясняются части сервера и клиента, которые разработчику необходимо понимать для использования персонализации.

## <a name="rank-api-client-application-sends-context-to-server"></a>API ранжирования: клиентское приложение отправляет контекст на сервер.

Клиентское приложение собирает пользовательский _Агент_браузера пользователя.

> [!div class="mx-imgBorder"]
> ![Создайте и запустите проект Хттпрекуестфеатуресексампле. Откроется окно браузера для вывода одностраничного приложения.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>API ранжирования: серверное приложение вызывает персонализацию

Это типичное веб-приложение .NET с клиентским приложением, большая часть кода стереотипногоной формы предоставляется вам. Код, не относящийся к персонализации, удаляется из следующих фрагментов кода, что позволяет сосредоточиться на коде, характерном для персонализации.

### <a name="create-personalizer-client"></a>Создание клиента персонализации

В **Startup.CS**сервера конечная точка и ключ персонализации используются для создания клиента персонализации. Клиентское приложение не обязано взаимодействовать с персонализацией в этом приложении, вместо этого она полагается на сервер для выполнения этих вызовов пакета SDK.

Код запуска .NET веб-сервера:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>Выбор оптимального действия

В **PersonalizerController.CS**сервера API сервера **женератеранк** содержит сводку по подготовке к вызову API ранжирования.

* Создать новый `eventId` для ранжирования вызова
* Получение списка действий
* Получение списка функций от пользователя и создание контекстных функций
* При необходимости задайте любые исключенные действия.
* API ранжирования вызовов, возвращение результатов клиенту

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

Формат JSON, отправленный в персонализацию, содержащий как действия (с функциями), так и текущие функции контекста, выглядит следующим образом:

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Возвращать Ревардактионид персонализации клиенту

API ранжирования Возвращает выбранное лучшее действие, **ревардактионид** на сервер.

Отображение действия, возвращенного в **ревардактионид**.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>Клиент отображает действие Ревардактионид

В этом руководстве `rewardActionId` отображается значение.

В вашем собственном приложении, которое может представлять собой точный текст, кнопку или раздел выделенной веб-страницы. Список возвращается для любого анализа оценки, а не его порядка. `rewardActionId`Должно отображаться только содержимое.

## <a name="reward-api-collect-information-for-reward"></a>API поощрений: получение сведений о награде

[Оценка вознаграждений](concept-rewards.md) должна быть тщательно спланирована, как и запланированные функции. Показатель вознаграждения обычно должен иметь значение от 0 до 1. Значение _может_ быть частично вычислено в клиентском приложении на основе поведения пользователя и частично на сервере на основе бизнес-логики и целей.

Если сервер не вызывает API наград в течение **времени ожидания** , настроенного в портал Azure для ресурса персонализации, то для этого события используется **вознаграждение по умолчанию** (также настроенное в портал Azure).

В этом примере приложения можно выбрать значение, чтобы увидеть, как вознаграждение влияет на выбор.

## <a name="additional-ways-to-learn-from-this-sample"></a>Дополнительные способы изучения этого примера

В примере используется несколько событий на основе времени, настроенных в портал Azure для ресурса персонализации. Поиграйте с этими значениями. Затем вернитесь к этому примеру веб-приложения, чтобы увидеть, как изменения влияют на вызовы ранжирования и вознаграждений:

* Время ожидания результата
* Частота обновления модели

Ниже приведены дополнительные параметры для воспроизведения.
* Результат по умолчанию
* Процент исследования


## <a name="clean-up-resources"></a>Очистка ресурсов

Когда вы завершите работу с этим руководством, очистите следующие ресурсы:

* Удалите каталог примера проекта.
* Удалите ресурс персонализации. Подумайте о ресурсе-члене персонализации, выделенном для действий и контекста. повторно используйте ресурс только в том случае, если вы по-прежнему используете домен субъекта продуктов AS Actions.


## <a name="next-steps"></a>Дальнейшие действия
* [Принцип работы Персонализатора](how-personalizer-works.md)
* [Функции](concepts-features.md): Общие сведения о функциях с помощью с действиями и контекстом
* [Вознаграждения](concept-rewards.md): Дополнительные сведения о вычислении вознаграждения