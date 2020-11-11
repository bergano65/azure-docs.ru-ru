---
title: Использование веб-приложения — Персонализатор
description: Настройте веб-приложение C# .NET с помощью цикла Персонализатора, чтобы предоставить пользователю правильное содержимое на основе действий (с признаками) и признаков контекста.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c004887e3883ae711974b544510dff16a98d4ef9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363924"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>Руководство по добавлению Персонализатора в веб-приложение .NET

Настройте веб-приложение C# .NET с помощью цикла Персонализатора, чтобы предоставить пользователю правильное содержимое на основе действий (с признаками) и признаков контекста.

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Настройка ключа и конечной точки Персонализатора.
> * Получение признаков.
> * Вызов API-интерфейсов ранжирования и вознаграждения.
> * Отображение популярных действий, обозначенных как _rewardActionId_.



## <a name="select-the-best-content-for-a-web-app"></a>Выбор лучшего содержимого для веб-приложения

Веб-приложение должно использовать Персонализатор при наличии списка _действий_ (определенный тип содержимого) на веб-странице, который необходимо настроить, чтобы отображался один верхний элемент (rewardActionId). Примерами списков действий являются новые статьи, расположения размещения кнопок и подбор слов для названий продуктов.

Вы отправляете список действий вместе с признаками контекста в цикл Персонализатора. Персонализатор выбирает одно лучшее действие, которое затем отображается в веб-приложении.

В рамках этого учебника в качестве действий будут использоваться типы пищи:

* макароны;
* мороженое;
* сок;
* cалат;
* попкорн;
* кофе;
* суп

Чтобы помочь Персонализатору узнать о ваших действиях, отправляйте _действия с признаками_ и _признаки контекста_ с каждым запросом API ранжирования.

**Признак** модели содержит информацию о действиях и контексте, которые могут быть агрегированы (сгруппированы) для всех членов базы пользователей веб-приложения. _Признак_ не является индивидуальным (как идентификатор пользователя) или высокоспециализированным (как точное время дня).

### <a name="actions-with-features"></a>Действия с признаками

Каждое действие (элемент содержимого) имеет признаки, позволяющие различить блюдо.

Они не настраиваются в рамках конфигурации цикла на портале Azure. Вместо этого они отправляются в виде объекта JSON с каждым вызовом API ранжирования. Это предоставляет действиям и их признакам возможность увеличиваться, изменяться и сжиматься в течение периода времени, что позволяет Персонализатору отслеживать тенденции.

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

Признаки контекста помогают Персонализатору понять контекст действий. Контекст этого примера приложения включает следующее:

* время суток — утро, полдень, вечер, ночь;
* вкусовые предпочтения пользователя — соленое, сладкое, горькое, кислое или острое;
* контекст браузера — агент пользователя, географическое расположение, источник ссылки.

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

## <a name="how-does-the-web-app-use-personalizer"></a>Как веб-приложение использует Персонализатор?

Веб-приложение использует Персонализатор, чтобы выбрать из списка предложенных продуктов лучшее действие. Для этого при каждом вызове API ранжирования отправляется следующая информация:
* **действия** с их признаками, такими как `taste` и `spiceLevel`;
* **признаки контекста** , например `time` дня, предпочтения `taste` пользователя, а также сведения об агенте пользователя браузера и признаки контекста;
* **исключаемые действия** , например сок;
* **код события** , который отличается для каждого вызова API ранжирования.

## <a name="personalizer-model-features-in-a-web-app"></a>Признаки модели Персонализатора в веб-приложении

Для Персонализатора необходимо предоставить признаки действий (содержимое) и текущий контекст (контекст пользователя и среды). Признаки используются, чтобы действия соответствовали текущему контексту в модели. Модель — это представление прошлых знаний Персонализатора о действиях, контексте и их признаках, которые позволяют принимать обоснованные решения.

Модель, включая признаки, обновляется по расписанию, заданному в разделе **Частота обновления модели** на портале Azure.

> [!CAUTION]
> Признаки в этом приложении предназначены для демонстрации признаков и их значений. Их не обязательно применять в веб-приложении.

### <a name="plan-for-features-and-their-values"></a>Планирование признаков и их значений

Признаки следует выбирать в соответствии с теми же параметрами планирования и разработки, которые вы будете применять ко всем схемам или моделям в системной архитектуре. Значения признаков можно задать с помощью бизнес-логики или сторонних систем. Они не должны быть высокоспециализированными, чтобы их можно было применить в группе или классе признаков.

### <a name="generalize-feature-values"></a>Подготовка значений признаков

#### <a name="generalize-into-categories"></a>Группирование по категориям

Это приложение использует `time` в качестве признака, однако группирует значения времени по категориям, таким как `morning`, `afternoon`, `evening` и `night`. Это пример использования информации о времени. Эти значения не заданы особым образом, например как `10:05:01 UTC+2`.

#### <a name="generalize-into-parts"></a>Разделение на части

Это приложение использует признаки HTTP-запросов из браузера. Цикл начинается с очень конкретной строки со всеми данными, например:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

Библиотека классов **HttpRequestFeatures** обобщает эту строку для объекта **userAgentInfo** , используя отдельные значения. Для всех значений, которые являются слишком конкретными, задается пустая строка. При отправке признаков контекста для запроса они имеют следующий формат JSON:

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

Для запуска приложения с помощью примера веб-приложения на основе браузера (со всеми предоставленными кодами) необходимо иметь указанные ниже установленные приложения.

Установите следующее программное обеспечение:

* [.NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) — пример внутреннего сервера использует .NET Core.
* [Node.js](https://nodejs.org/) — от этого приложения зависят клиент и внешний интерфейс.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) или [.NET Core CLI](/dotnet/core/tools/) — для разработки и запуска приложения предлагается использовать среду разработки Visual Studio 2019 или .NET Core CLI.

### <a name="set-up-the-sample"></a>Настройка примера
1. Клонируйте репозиторий примеров Персонализатора Azure.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Перейдите в раздел _samples/HttpRequestFeatures_ , чтобы открыть решение `HttpRequestFeaturesExample.sln`.

    После соответствующего запроса разрешите Visual Studio обновить пакет .NET для Персонализатора.

### <a name="set-up-azure-personalizer-service"></a>Настройка службы "Персонализатор" Azure

1. [Создайте ресурс "Персонализатор"](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) на портале Azure.

1. На портале Azure найдите `Endpoint`, а также ключ `Key1` или `Key2` (подойдут оба) на вкладке **Keys and Endpoints** (Ключи и конечные точки). Это ваши значения `PersonalizerServiceEndpoint` и `PersonalizerApiKey`.
1. Укажите значение `PersonalizerServiceEndpoint` в файле **appsettings.json**.
1. Настройте `PersonalizerApiKey` в качестве [секрета приложения](/aspnet/core/security/app-secrets) одним из следующих способов:

    * Если вы используете .NET Core CLI, можно выполнить команду `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"`.
    * Если вы используете Visual Studio, щелкните правой кнопкой мыши проект и выберите пункт меню **Управление секретами пользователей** , чтобы настроить ключи Персонализатора. После этого Visual Studio откроет файл `secrets.json`, в котом можно добавить ключи следующим образом:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>Запуск примера

Создайте и запустите HttpRequestFeaturesExample с помощью одного из следующих методов:

* Visual Studio 2019: нажмите клавишу **F5**.
* .NET Core CLI: выполните команду `dotnet build`, а затем — `dotnet run`.

С помощью браузера вы можете отправлять запросы на ранжирование и вознаграждения и просматривать ответы на них, а также признаки HTTP-запроса, извлеченные из вашей среды.

> [!div class="mx-imgBorder"]
> ![Снимок экрана: пример HttpRequestFeaturesExample в браузере](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Демонстрация цикла Персонализатора

1. Нажмите кнопку **Generate new Rank Request** (Создать запрос ранжирования), чтобы создать объект JSON для вызова API ранжирования. При этом будут созданы действия (с признаками) и признаки контекста, а также отображены значения, чтобы вы могли увидеть, как будет выглядеть файл JSON.

    В вашем собственном будущем приложении создание действий и признаков может выполняться на стороне клиента, сервера или с использованием обоих этих вариантов, а также посредством вызовов к другим службам.

1. Выберите **Send Rank Request** (Отправить запрос ранжирования), чтобы отправить объект JSON на сервер. Сервер вызывает API ранжирования Персонализатора. Затем сервер получает ответ и возвращает высокоприоритетное действие клиенту для отображения.

1. Задайте значение вознаграждения, а затем нажмите кнопку **Send Reward Request** (Отправить запрос вознаграждения). Если вы не меняли значение вознаграждения, приложение клиента будет всегда отправлять значение `1` Персонализатору.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана: раздел запроса вознаграждения](./media/tutorial-web-app/reward-score-api-call.png)

    В вашем будущем приложении создание оценки вознаграждения может выполняться после сбора информации на основе данных поведения пользователя на стороне клиента, а также бизнес-логики на стороне сервера.

## <a name="understand-the-sample-web-app"></a>Общие сведения о примере веб-приложения

Пример веб-приложения имеет сервер **C# .NET** , который управляет сбором признаков, а также отправляет вызовы HTTP в конечную точку Персонализатора и получает их из нее.

В примере веб-приложения используется **внешнее клиентское приложение удаления** для записи признаков, обработки действий пользовательского интерфейса, например нажатий кнопок, и отправки данных на сервер .NET.

В следующих разделах подробно описаны компоненты сервера и клиента, которые помогут разработчикам лучше понять, как использовать Персонализатор.

## <a name="rank-api-client-application-sends-context-to-server"></a>API ранжирования: клиентское приложение отправляет контекст на сервер

Клиентское приложение собирает данные об _агенте пользователя_ браузера.

> [!div class="mx-imgBorder"]
> ![Создание и запуск проекта HTTPRequestFeaturesExample. Открытое окно браузера с однострочным приложением.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>API ранжирования: приложение сервера вызывает Персонализатор

Это типичное веб-приложение .NET с клиентским приложением, большая часть шаблонного кода которого уже вам доступна. Любой код, который не относится к Персонализатору, будет удален из указанных ниже фрагментов кода. Это позволит вам сосредоточиться на конкретном коде Персонализатора.

### <a name="create-personalizer-client"></a>Создание клиента Персонализатора

В файле **Startup.cs** сервера используется конечная точка и ключ Персонализатора для создания его клиента. Клиентскому приложению не нужно взаимодействовать с Персонализатором в этом приложении, вместо того оно использует сервер, чтобы отправить вызовы SDK.

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

В файле **PersonalizerController.cs** сервера API сервера **GenerateRank** обобщены сведения об этапах подготовки для вызова API ранжирования

* Создание `eventId` для вызова ранжирования.
* Получение списка действий.
* Получение списка признаков от пользователя и создание признаков контекста.
* Настройка исключенных действий (при необходимости).
* Вызов API ранжирования и возвращение результатов клиенту.

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

Файл JSON, отправленный в Персонализатор, содержащий действия (с признаками) и поточные признаки контекста, выглядит следующим образом:

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

### <a name="return-personalizer-rewardactionid-to-client"></a>Возвращение значения rewardActionId Персонализатора клиенту

API ранжирования возвращает выбранное оптимальное действие **rewardActionId** на сервер.

Отобразите действие, возвращенное в **rewardActionId**.

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

### <a name="client-displays-the-rewardactionid-action"></a>Отображение клиентом действия rewardActionId

В этом учебнике отображается значение `rewardActionId`.

В вашем собственном будущем приложении это значение может представлять собой некоторый точный текст, кнопку или раздел выделенной веб-страницы. Список возвращается для любого пост-анализа оценок, а не для упорядочения содержимого. Должно отображаться только содержимое `rewardActionId`.

## <a name="reward-api-collect-information-for-reward"></a>API вознаграждения: получение сведений о вознаграждении

[Оценку вознаграждения](concept-rewards.md) следует тщательно планировать, точно так же, как и признаки. Оценка вознаграждения обычно должна иметь значение от 0 до 1. Значение _можно_ частично вычислить в клиентском приложении на основе поведения пользователя и частично на сервере на основе бизнес-логики и целей.

Если сервер не вызывает API поощрения в течение **времени ожидания вознаграждения** , установленного на портале Azure для ресурса "Персонализатор", для этого события используется **вознаграждение по умолчанию** (также настроенное на портале Azure).

В этом примере приложения вы можете выбрать значение, чтобы увидеть, как вознаграждения влияют на выбранные элементы.

## <a name="additional-ways-to-learn-from-this-sample"></a>Дополнительные способы изучения этого примера

В примере используется несколько событий на основе времени, настроенные на портале Azure для ресурса "Персонализатор". Поэкспериментируйте с этими значениями, а затем вернитесь к этому примеру веб-приложения, чтобы увидеть, как изменения влияют на вызовы ранжирования и вознаграждения.

* Время ожидания результата
* Частота обновления модели

Другие возможности, с которыми вы можете поэкспериментировать:
* Результат по умолчанию
* Процент исследования.


## <a name="clean-up-resources"></a>Очистка ресурсов

Когда вы завершите работу с этим учебником, очистите следующие ресурсы:

* Удалите пример каталога проекта.
* Удалите ресурс "Персонализатор". Используйте ресурс "Персонализатор" только для действий и контекста. Ресурс следует использовать повторно, только если вы используете продукты в качестве предметной области действий.


## <a name="next-steps"></a>Дальнейшие действия
* [Принцип работы Персонализатора](how-personalizer-works.md)
* [Признаки](concepts-features.md): сведения о концепциях признаков, использующиеся для действий и контекста.
* [Вознаграждения](concept-rewards.md): сведения о вычислении оценки вознаграждения.