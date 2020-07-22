---
title: Использование Azure Digital двойников для обновления карт Azure Maps
titleSuffix: Azure Digital Twins
description: Узнайте, как создать функцию Azure, которая может использовать двойника Graph и уведомления Azure Digital двойников для обновления сведений, отображаемых в Azure Maps.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/3/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 8f3e670a4f2a49bcce48be1ba0452a36cbf96df1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392324"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Использование Azure Digital двойников для обновления карт Azure Maps

В этой статье рассматриваются шаги, необходимые для использования данных Azure Digital двойников для обновления сведений, отображаемых на *карте* с помощью [Azure Maps](../azure-maps/about-azure-maps.md). Azure Digital двойников хранит Граф отношений устройств IoT и направляет данные телеметрии в разные конечные точки, делая ее идеальной службой для обновления информационных наложений на картах.

В этом практические руководства будут рассмотрены следующие вопросы.

1. Настройка экземпляра Azure Digital двойников для отправки событий обновления двойника в функцию в функциях [Azure](../azure-functions/functions-overview.md).
2. Создание функции Azure для обновления "Azure Maps" функции сопоставления.
3. Сведения о хранении идентификатора сопоставления и идентификатора состояния компонентов в графе Azure Digital двойников.

### <a name="prerequisites"></a>Предварительные условия

* Следуйте указаниям в руководстве по цифровому двойников Azure [: подключение комплексного решения](./tutorial-end-to-end.md).
    * Вы будете расширять эту двойника с помощью дополнительной конечной точки и маршрута. Вы также добавите в приложение функции другую функцию из этого руководства. 
* Следуйте указаниям в руководстве по Azure Maps [. Создайте карты с помощью Azure Maps Creator](../azure-maps/tutorial-creator-indoor-maps.md) , чтобы создать Azure Mapsную карту с набором *состояний компонентов*.
    * [Feature статесетс](../azure-maps/creator-indoor-maps.md#feature-statesets) — это коллекции динамических свойств (состояний), назначенных функциям набора данных, например комнатам или оборудованию. В приведенном выше руководстве по Azure Mapsю набор состояния компонента сохраняет состояние комнаты, которое будет отображаться на карте.
    * Вам потребуется *идентификатор* и идентификатор *подписки*Azure Maps.

### <a name="topology"></a>Топология

На рисунке ниже показано, где элементы интеграции карт в этом руководстве соответствуют более широкому комплексному сценарию Azure Digital двойников.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="Представление служб Azure в комплексном сценарии, в котором выделяется элемент интеграции Maps" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>Создание функции для обновления схемы при двойников обновления

Сначала вы создадите маршрут в Azure Digital двойников, чтобы перенаправить все события обновления двойника в раздел сетки событий. Затем вы используете функцию Azure для чтения этих сообщений об обновлениях и обновляете набор состояний компонентов в Azure Maps. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Создание маршрута и фильтрация для двойника уведомлений об обновлении

Экземпляры Azure Digital двойников могут создавать события обновления двойника при каждом обновлении состояния двойника. [Руководство по цифровому двойников Azure. подключение к комплексному решению](./tutorial-end-to-end.md) , описанному выше, рассматривается в сценарии, в котором для обновления атрибута температуры, прикрепленного к двойника комнаты, используется термометр. Вы будете расширять это решение, подписавшись на уведомления об обновлениях для двойников и используя эти сведения для обновления карт.

Этот шаблон считывает данные из комнаты двойника напрямую, а не с устройства IoT, что дает нам возможность изменить базовый источник данных для температуры без необходимости обновления нашей логики сопоставления. Например, можно добавить несколько термометров или задать для этой комнаты общий доступ к термометру в другой комнате, не требуя обновления логики карт.

1. Создайте раздел "Сетка событий", который будет принимать события из нашего экземпляра Digital двойников для Azure.
    ```azurecli
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Создайте конечную точку, чтобы связать раздел "Сетка событий" с цифровым двойников Azure.
    ```azurecli
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. Создайте маршрут в Azure Digital двойников, чтобы отправить события обновления двойника в конечную точку.
    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "{ "endpointId": "<endpoint-ID>","filter": "type = 'Microsoft.DigitalTwins.Twin.Update'"}"
    ```

## <a name="create-an-azure-function-to-update-maps"></a>Создание функции Azure для обновления карт

Вы создадите функцию, активируемую с помощью функции "Сетка событий" в приложении-функции, из [комплексного руководства](./tutorial-end-to-end.md). Эта функция выполнит распаковку этих уведомлений и отправит обновления в Azure Maps наборе состояний компонентов, чтобы обновить температуру одной комнаты. 

Справочные сведения см. в следующем документе: триггер службы " [Сетка событий Azure" для функций Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger).

Замените код функции следующим кодом. Он фильтрует только обновления пространства двойников, считывает обновленную температуру и отправляет эти сведения в Azure Maps.

```C#
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Threading.Tasks;
using System.Net.Http;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoMaps
    {   //Read maps credentials from application settings on function startup
        private static string statesetID = Environment.GetEnvironmentVariable("statesetID");
        private static string subscriptionKey = Environment.GetEnvironmentVariable("subscription-key");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("ProcessDTUpdatetoMaps")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
            log.LogInformation("Reading event from twinID:" + eventGridEvent.Subject.ToString() + ": " +
                eventGridEvent.EventType.ToString() + ": " + message["data"]);

            //Parse updates to "space" twins
            if (message["data"]["modelId"].ToString() == "dtmi:contosocom:DigitalTwins:Space;1")
            {   //Set the ID of the room to be updated in our map. 
                //Replace this line with your logic for retrieving featureID. 
                string featureID = "UNIT103";

                //Iterate through the properties that have changed
                foreach (var operation in message["data"]["patch"])
                {
                    if (operation["op"].ToString() == "replace" && operation["path"].ToString() == "/Temperature")
                    {   //Update the maps feature stateset
                        var postcontent = new JObject(new JProperty("States", new JArray(
                            new JObject(new JProperty("keyName", "temperature"),
                                 new JProperty("value", operation["value"].ToString()),
                                 new JProperty("eventTimestamp", DateTime.Now.ToString("s"))))));

                        var response = await httpClient.PostAsync(
                            $"https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetID}&featureID={featureID}&subscription-key={subscriptionKey}",
                            new StringContent(postcontent.ToString()));

                        log.LogInformation(await response.Content.ReadAsStringAsync());
                    }
                }
            }
        }
    }
}
```

В приложении функции необходимо задать две переменные среды. Один из них — [ключ первичной подписки Azure Maps](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account), а второй — [идентификатор своего Azure Maps](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset).

```azurecli-interactive
az functionapp config appsettings set --settings "subscription-key=<your-Azure-Maps-primary-subscription-key> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
az functionapp config appsettings set --settings "statesetID=<your-Azure-Maps-stateset-ID> -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

### <a name="view-live-updates-on-your-map"></a>Просмотр динамических обновлений на карте

Чтобы увидеть температуру в динамическом обновлении, выполните следующие действия:

1. Начните отправлять смоделированные данные IoT, запустив проект **девицесимулатор** из учебника по цифровым двойников Azure [: подключение комплексного решения](tutorial-end-to-end.md). Инструкции для этого см. в разделе [*Настройка и запуск моделирования*](././tutorial-end-to-end.md#configure-and-run-the-simulation) .
2. Используйте [модуль **Azure Maps** ](../azure-maps/how-to-use-indoor-module.md) для отображения карт, созданных в Azure Maps Creator.
    1. Скопируйте HTML-код из [*примера: используйте модуль Maps*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) в учебнике по картам для подразделов [: используйте модуль map Maps (Azure Maps](../azure-maps/how-to-use-indoor-module.md) ) к локальному файлу.
    1. Замените *тилесетид* и *статесетид* в локальном HTML-файле своими значениями.
    1. Откройте этот файл в браузере.

Оба образца отправляют температуру в совместимом диапазоне, поэтому на карте должен отображаться цвет для обновления комнаты 121 примерно каждые 30 секунд.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="Схема Office, в которой отображается комната 121, окрашенный оранжевый":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Хранение сведений о картах в цифровом двойников Azure

Теперь, когда у вас есть жестко закодированное решение для обновления сведений о картах, вы можете использовать граф Azure Digital двойников для хранения всей информации, необходимой для обновления карты. Это будет включать идентификатор набора состояний, идентификатор подписки и идентификатор функции для каждой карты и расположения соответственно. 

Решение для этого конкретного примера будет включать в себя обновление каждого пространства верхнего уровня, чтобы иметь идентификатор и атрибут идентификатора подписки, а для каждой комнаты — идентификатор компонента. Эти значения необходимо задать один раз при инициализации графа двойника, а затем запросить эти значения для каждого события обновления двойника.

В зависимости от конфигурации топологии вы сможете хранить эти три атрибута на разных уровнях, соотнесенных с степенью детализации вашей схемы.

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения об управлении, обновлении и извлечении данных из графа двойников см. в следующих статьях:

* [Практические руководства. Управление цифровыми двойников](./how-to-manage-twin.md)
* [Пошаговое руководство. запрос графа двойника](./how-to-query-graph.md)