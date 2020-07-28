---
title: Интеграция со службой "аналитика временных рядов"
titleSuffix: Azure Digital Twins
description: См. статью Настройка маршрутов событий из Azure Digital двойников в службу "аналитика временных рядов Azure".
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9be44d29ab08a7404bfb14d691bb5bb339284714
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131605"
---
# <a name="integrate-digital-twins-with-azure-time-series-insights"></a>Интеграция Digital двойников со службой "аналитика временных рядов Azure"

В этом справочнике вы узнаете, как интегрировать Azure Digital двойников со службой ["аналитика временных рядов Azure" (TSI)](../time-series-insights/overview-what-is-tsi.md). Это решение позволит собирать и анализировать исторические данные о решении IoT. Azure Digital двойников отлично подходит для передачи данных в службу "аналитика временных рядов", так как позволяет сопоставлять несколько потоков данных и стандартизировать информацию перед их отправкой в службу "аналитика временных рядов". 

## <a name="solution-architecture"></a>Архитектура решения

Вы будете присоединить службу "аналитика временных рядов" к Azure Digital двойников по указанному ниже пути.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Представление служб Azure в комплексном сценарии, выделяющий аналитику временных рядов" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Обязательные условия

* Вам потребуется экземпляр Azure Digital двойников, который позволяет обновлять сведения о двойникае несколько раз, чтобы увидеть, что данные отписываются в службе "аналитика временных рядов". 
    * Если у вас ее нет, следуйте указаниям в руководстве по цифровому двойников Azure [*: подключение комплексного решения*](./tutorial-end-to-end.md) для настройки экземпляра Azure Digital двойников и виртуального устройства IOT для создания двойника изменений.

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Создание маршрута и фильтрация для двойника уведомлений об обновлении

Экземпляры Azure Digital двойников могут создавать [события обновления двойника](how-to-interpret-event-data.md) при каждом обновлении состояния двойника. Будет создан маршрут, который будет направлять эти события обновления в концентратор событий для дальнейшей обработки.

Руководство по работе с цифровыми Двойниковми Azure [*: подключение комплексного решения*](./tutorial-end-to-end.md) в рамках сценария, в котором термометр используется для обновления атрибута температуры, прикрепленного к двойника комнаты. Этот шаблон использует обновления двойника, а не пересылает телеметрию с устройства IoT, что дает возможность изменить базовый источник данных без необходимости обновления логики аналитики временных рядов.

1. Создайте пространство имен концентратора событий, которое будет принимать события из своего экземпляра цифрового Двойникова Azure. Вы можете использовать приведенные ниже инструкции Azure CLI или воспользоваться портал Azure: [*Краткое руководство. Создание концентратора событий с помощью портал Azure*](../event-hubs/event-hubs-create.md).

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Создайте концентратор событий.

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```

3. Создайте [правило авторизации](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) с разрешениями Send и Receive.

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

4. Создайте конечную точку, чтобы связать раздел "Сетка событий" с цифровым двойников Azure.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace> --eventhub <twins event hub name> --eventhub-policy <twins auth rule> -n <your-Azure-Digital-Twins-instance-name>
    ```

5. Создайте маршрут в Azure Digital двойников, чтобы отправить события обновления двойника в конечную точку. Фильтр в этом маршруте позволит передавать только сообщения обновления двойника в конечную точку.

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Hub-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function"></a>Создание функции Azure 

Далее предстоит создать функцию, активируемую концентраторами событий, в новом приложении-функции — приложении функции из сквозного учебника ([*Учебник: подключение комплексного решения*](./tutorial-end-to-end.md)). Эта функция преобразует эти обновления из исходной формы в документы исправления JSON в объекты JSON, содержащие только обновленные и добавленные значения из двойников.

Дополнительные сведения об использовании концентраторов событий с функциями Azure см. в статье [*триггер концентраторов событий Azure для функций Azure*](../azure-functions/functions-bindings-event-hubs-trigger.md).

В опубликованном приложении функции замените код функции следующим кодом.

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-fx-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("alkarche-tsi-demo-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

После этого функция отправляет объекты JSON, которые он создает, во второй концентратор событий, который будет подключаться к службе "аналитика временных рядов".

## <a name="send-telemetry-to-an-event-hub"></a>Отправка данных телеметрии в концентратор событий

Теперь вы создадите второй концентратор событий и настроите функцию для потоковой передачи выходных данных в этот концентратор событий. Затем этот концентратор событий будет подключен к службе "аналитика временных рядов".

### <a name="create-an-event-hub"></a>Создание концентратора событий

Вы можете использовать приведенные ниже инструкции Azure CLI или воспользоваться портал Azure: [*Краткое руководство. Создание концентратора событий с помощью портал Azure*](../event-hubs/event-hubs-create.md).

1. Подготовьте пространство имен концентратора событий и имя группы ресурсов ранее 

2. Создание концентратора событий
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <TSI event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```
3. Создание [правила авторизации](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) с разрешениями Send и Receive
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <event hub name> --name <TSI auth rule>
    ```

### <a name="configure-your-function"></a>Настройка функции

В приложении-функции необходимо задать одну переменную среды, которая будет содержать строку подключения к концентратору событий.

#### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Настройка строки подключения концентратора событий "аналитика временных рядов"

1. Получите [строку подключения концентратора событий](../event-hubs/event-hubs-get-connection-string.md) для правил авторизации, созданных ранее для центра "аналитика временных рядов".

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

2. В приложении функции создайте параметр приложения, содержащий строку подключения:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

#### <a name="set-the-twins-event-hub-connection-string"></a>Задание строки подключения концентратора событий двойников

1. Получите [строку подключения концентратора событий](../event-hubs/event-hubs-get-connection-string.md) для правил авторизации, созданных ранее для центра двойников.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. В приложении функции создайте параметр приложения, содержащий строку подключения:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Создание и подключение экземпляра Time Series Insights

Далее вы настроите экземпляр Time Series Insights для получения данных из второго концентратора событий. Дополнительные сведения об этом процессе см [ *. в разделе Учебник. Настройка среды Azure Time Series Insights Gen2 PAYG*](../time-series-insights/tutorials-set-up-tsi-environment.md)

1. В портал Azure приступите к созданию ресурса "аналитика временных рядов". 
    1. Выберите ценовую категорию **PAYG (Предварительная версия)** .
    2. Для этого окружения необходимо выбрать идентификатор временного ряда. Идентификатор временных рядов может содержать до трех значений, которые будут использоваться для поиска данных в аналитике временных рядов. В этом руководстве можно использовать **$dtId**. Дополнительные сведения о выборе идентификатора [*временных рядов*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid)см. в этой статье.
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="Пользовательский интерфейс портала создания для среды аналитика временных рядов. Выбрана ценовая категория PAYG (Предварительная версия) и имя свойства идентификатора временных рядов $dtId":::

2. Выберите **Далее: источник события** и выберите сведения о концентраторах событий выше. Также потребуется создать группу потребителей концентраторов событий.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Пользовательский интерфейс портала создания для источника событий среды аналитика временных рядов. Вы создаете источник событий со сведениями о концентраторе событий, указанными выше. Вы также создаете новую группу потребителей.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Начало отправки данных IoT в Azure Digital двойников

Чтобы начать отправку данных в аналитику временных рядов, необходимо начать обновление свойств Digital двойника с изменяемыми значениями данных. Используйте команду [AZ DT двойника Update](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) .

Если вы используете комплексное руководство для помощи в настройке среды, вы можете начать отправку смоделированных данных IoT, запустив `DeviceSimulator` проект из учебника по цифровым двойников Azure [*: подключение комплексного решения*](tutorial-end-to-end.md). Инструкции см. в разделе [*Настройка и запуск модели моделирования*](tutorial-end-to-end.md#configure-and-run-the-simulation) руководства.

## <a name="visualize-your-data-in-time-series-insights"></a>Визуализация данных в Time Series Insights

Теперь данные должны передаваться в экземпляр "аналитика временных рядов", готовый для анализа. Выполните следующие действия, чтобы просмотреть данные, поступающие в.

1. Откройте экземпляр Time Series Insights в портал Azure. Посетите URL-адрес обозревателя "аналитика временных рядов", показанный в обзоре.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Щелкните URL-адрес обозревателя аналитика временных рядов на вкладке Обзор среды аналитика временных рядов.":::

2. В обозревателе вы увидите три двойников, показанные слева. Щелкните **thermostat67**, выберите **patch_value**и нажмите кнопку **добавить**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Щелкните * * thermostat67 * *, выберите * * температура * * и щелкните * * Добавить * *.":::

3. Теперь вы увидите начальное считывание температуры из термостата, как показано ниже. То же самое считывание температуры обновляется для room21 и floor1, и вы можете визуализировать эти потоки данных совместно.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Начальные данные температуры отображаются в обозревателе TSI. Это строка случайных значений между 68 и 85":::

4. Если вы разрешаете выполнение симуляции намного дольше, Визуализация будет выглядеть примерно так:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Данные температуры для каждого двойника отображаются в виде трех параллельных линий различных цветов.":::

## <a name="next-steps"></a>Дальнейшие действия

Цифровые двойников по умолчанию хранятся в виде плоской иерархии в службе "аналитика временных рядов", но их можно расширить с помощью сведений о модели и многоуровневого дерева для Организации. Дополнительные сведения об этом процессе см. в следующих статье: 

* [*Учебник. Определение и применение модели*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Вы можете написать настраиваемую логику для автоматического предоставления этих сведений с помощью модели и данных графа, уже сохраненных в Azure Digital двойников. Дополнительные сведения об управлении, обновлении и извлечении данных из графа двойников см. в следующих статьях:

* [*Практические руководства. Управление цифровым двойника*](./how-to-manage-twin.md)
* [*Пошаговое руководство. запрос графа двойника*](./how-to-query-graph.md)
