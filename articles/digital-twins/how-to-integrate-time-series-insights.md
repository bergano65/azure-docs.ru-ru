---
title: Интеграция со службой "Аналитика временных рядов Azure"
titleSuffix: Azure Digital Twins
description: См. статью Настройка маршрутов событий из Azure Digital двойников в службу "аналитика временных рядов Azure".
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c6c5c9b00ec3309638a7c5618e5995c8c5f07b11
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564378"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Интеграция Azure Digital двойников со службой "аналитика временных рядов Azure"

В этой статье вы узнаете, как интегрировать Azure Digital двойников со службой ["аналитика временных рядов Azure" (TSI)](../time-series-insights/overview-what-is-tsi.md).

Решение, описанное в этой статье, позволит собирать и анализировать исторические данные о решении IoT. Azure Digital двойников отлично подходит для передачи данных в службу "аналитика временных рядов", так как позволяет сопоставлять несколько потоков данных и стандартизировать информацию перед их отправкой в службу "аналитика временных рядов". 

## <a name="prerequisites"></a>предварительные требования

Прежде чем можно будет настроить связь со службой "аналитика временных рядов", необходимо иметь **экземпляр Digital двойников для Azure**. Этот экземпляр следует настроить с возможностью обновлять сведения о цифровом двойника на основе данных, так как вам потребуется обновить информацию двойника несколько раз, чтобы увидеть, что данные отписываются в службе "аналитика временных рядов". 

Если вы еще не настроили этот параметр, вы можете создать его, следуя руководству по цифровому двойников Azure [*: подключение комплексного решения*](./tutorial-end-to-end.md). В этом руководстве описано, как настроить экземпляр Digital двойников для Azure, который работает с виртуальным устройством IoT, чтобы активировать цифровые двойника обновления.

## <a name="solution-architecture"></a>Архитектура решения

Вы будете присоединить службу "аналитика временных рядов" к Azure Digital двойников по указанному ниже пути.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Представление служб Azure в комплексном сценарии, выделяющий аналитику временных рядов" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Создание маршрута и фильтрация для двойника уведомлений об обновлении

Экземпляры Azure Digital двойников могут создавать [события обновления двойника](how-to-interpret-event-data.md) при каждом обновлении состояния двойника. В этом разделе вы создадите [**маршрут событий**](concepts-route-events.md) Azure Digital двойников, который будет направлять эти события обновления в [концентраторы событий](../event-hubs/event-hubs-about.md) Azure для дальнейшей обработки.

Руководство по работе с цифровыми Двойниковми Azure [*: подключение к комплексному решению*](./tutorial-end-to-end.md) с использованием термометра для обновления атрибута температуры цифрового двойника, представляющего комнату. Этот шаблон основан на обновлениях двойника, а не на пересылке телеметрических данных с устройства IoT, что дает вам возможность изменить базовый источник, не требуя обновления логики "аналитика временных рядов".

1. Сначала создайте пространство имен концентратора событий, которое будет принимать события из своего экземпляра Azure Digital двойников. Вы можете использовать приведенные ниже инструкции Azure CLI или воспользоваться портал Azure: [*Краткое руководство. Создание концентратора событий с помощью портал Azure*](../event-hubs/event-hubs-create.md).

    ```azurecli
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Создайте концентратор событий в пространстве имен.

    ```azurecli
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Создайте [правило авторизации](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) с разрешениями Send и Receive.

    ```azurecli
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Создайте [конечную точку](concepts-route-events.md#create-an-endpoint) Azure Digital двойников, которая связывает раздел "Сетка событий" с вашим экземпляром Azure Digital двойников.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above> -n <your Azure Digital Twins instance name>
    ```

5. Создайте [маршрут](concepts-route-events.md#create-an-event-route) в Azure Digital двойников, чтобы отправить события обновления двойника в конечную точку. Фильтр в этом маршруте позволит передавать только сообщения обновления двойника в конечную точку.

    >[!NOTE]
    >В Cloud Shell присутствует **известная проблема**, которая затрагивает такие группы команд: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Чтобы устранить эту проблему, выполните `az login` в Cloud Shell перед выполнением команды или используйте [локальный CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) вместо Cloud Shell. Дополнительные сведения см. в статье [*Устранение неполадок: известные проблемы в Azure Digital Twins*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

Прежде чем продолжить, запишите *пространство имен концентраторов событий* и *группу ресурсов*, так как они будут использоваться для создания другого концентратора событий далее в этой статье.

## <a name="create-an-azure-function"></a>Создание функции Azure 

Далее вы создадите функцию, активируемую концентраторами событий, в приложении-функции. Вы можете использовать приложение-функцию, созданное в комплексном учебнике ([*руководство по подключению комплексного решения*](./tutorial-end-to-end.md)) или к своему собственному интерфейсу. 

Эта функция преобразует события обновления двойника из их исходной формы в документы исправления JSON в объекты JSON, содержащие только обновленные и добавленные значения из двойников.

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
            [EventHubTrigger("twins-event-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("tsi-event-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
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

Позже вы также настроите некоторые переменные среды, которые эта функция будет использовать для подключения к вашим концентраторам событий.

## <a name="send-telemetry-to-an-event-hub"></a>Отправка данных телеметрии в концентратор событий

Теперь вы создадите второй концентратор событий и настроите функцию для потоковой передачи выходных данных в этот концентратор событий. Затем этот концентратор событий будет подключен к службе "аналитика временных рядов".

### <a name="create-an-event-hub"></a>Создание концентратора событий

Чтобы создать второй концентратор событий, можно либо использовать приведенные ниже инструкции Azure CLI, либо воспользоваться портал Azure: [*Краткое руководство. Создание концентратора событий с помощью портал Azure*](../event-hubs/event-hubs-create.md).

1. Подготовьте *пространство имен концентраторов событий* и имя *группы ресурсов* из ранее в этой статье.

2. Создание нового концентратора событий
    ```azurecli
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Создание [правила авторизации](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) с разрешениями Send и Receive
    ```azurecli
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>Настройка функции

Далее необходимо задать в приложении функции переменные среды, которые содержат строки подключения для созданных концентраторов событий.

### <a name="set-the-twins-event-hub-connection-string"></a>Задание строки подключения концентратора событий двойников

1. Получите [строку подключения концентратора событий](../event-hubs/event-hubs-get-connection-string.md)двойников, используя правила авторизации, созданные ранее для центра двойников.

    ```azurecli
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Используйте строку подключения в качестве результата, чтобы создать параметр приложения в приложении-функции, который содержит строку подключения:

    ```azurecli
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Настройка строки подключения концентратора событий "аналитика временных рядов"

1. Получите [строку подключения концентратора событий](../event-hubs/event-hubs-get-connection-string.md)TSI, используя правила авторизации, созданные ранее для центра "аналитика временных рядов".

    ```azurecli
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. В приложении функции создайте параметр приложения, содержащий строку подключения:

    ```azurecli
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Создание и подключение экземпляра Time Series Insights

Далее вы настроите экземпляр Time Series Insights для получения данных из второго концентратора событий. Выполните приведенные ниже действия и дополнительные сведения об этом процессе см. в разделе [*учебник. Настройка среды Gen2 PAYG для службы "аналитика временных рядов Azure"*](../time-series-insights/tutorials-set-up-tsi-environment.md).

1. В портал Azure приступите к созданию ресурса "аналитика временных рядов". 
    1. Выберите ценовую категорию **PAYG (Предварительная версия)** .
    2. Для этого окружения необходимо выбрать **идентификатор временного ряда** . Идентификатор временных рядов может содержать до трех значений, которые будут использоваться для поиска данных в аналитике временных рядов. В этом руководстве можно использовать **$dtId**. Дополнительные сведения о выборе идентификатора [*временных рядов*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid)см. в этой статье.
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="Пользовательский интерфейс портала создания для среды "аналитика временных рядов". Выбрана ценовая категория PAYG (Предварительная версия) и имя свойства идентификатора временных рядов $dtId":::

2. Выберите **Далее: источник события** и выберите сведения о концентраторах событий выше. Также потребуется создать группу потребителей концентраторов событий.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Пользовательский интерфейс портала создания для источника событий среды "аналитика временных рядов". Вы создаете источник событий со сведениями о концентраторе событий, указанными выше. Вы также создаете новую группу потребителей.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Начало отправки данных IoT в Azure Digital двойников

Чтобы начать отправку данных в службу "аналитика временных рядов", необходимо начать обновление свойств Digital двойника в Azure Digital двойников, изменив значения данных. Используйте команду [AZ DT двойника Update](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) .

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

Если вы используете полное руководство ([*руководство по подключению комплексного решения*](tutorial-end-to-end.md)) для помощи в настройке среды, можно начать отправку смоделированных данных IOT, запустив проект *девицесимулатор* из примера. Инструкции см. в разделе [*Настройка и запуск модели моделирования*](tutorial-end-to-end.md#configure-and-run-the-simulation) руководства.

## <a name="visualize-your-data-in-time-series-insights"></a>Визуализация данных в Time Series Insights

Теперь данные должны передаваться в экземпляр "аналитика временных рядов", готовый для анализа. Выполните следующие действия, чтобы просмотреть данные, поступающие в.

1. Откройте экземпляр службы "аналитика временных рядов" в [портал Azure](https://portal.azure.com) (вы можете найти имя своего экземпляра на панели поиска портала). Посетите *URL-адрес обозревателя "аналитика временных рядов* ", показанный в обзоре экземпляра.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Выберите URL-адрес обозревателя "аналитика временных рядов" на вкладке "Обзор" среды "аналитика временных рядов".":::

2. В обозревателе вы увидите три двойников из Azure Digital двойников, показанные слева. Выберите _**thermostat67**_, выберите **температура**и нажмите кнопку **добавить**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Выберите * * thermostat67 * *, выберите * * температура * * и нажмите * * Добавить * *.":::

3. Теперь вы увидите начальное считывание температуры из термостата, как показано ниже. То же самое считывание температуры обновляется для *room21* и *floor1*, и вы можете визуализировать эти потоки данных совместно.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Начальные данные температуры отображаются в обозревателе TSI. Это строка случайных значений между 68 и 85":::

4. Если вы разрешаете выполнение симуляции намного дольше, Визуализация будет выглядеть примерно так:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Данные температуры для каждого двойника отображаются в виде трех параллельных линий различных цветов.":::

## <a name="next-steps"></a>Дальнейшие действия

Цифровые двойников по умолчанию хранятся в виде плоской иерархии в службе "аналитика временных рядов", но их можно расширить с помощью сведений о модели и многоуровневого дерева для Организации. Дополнительные сведения об этом процессе см. в следующих статье: 

* [*Учебник. Определение и применение модели*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Вы можете написать настраиваемую логику для автоматического предоставления этих сведений с помощью модели и данных графа, уже сохраненных в Azure Digital двойников. Дополнительные сведения об управлении, обновлении и извлечении данных из графа двойников см. в следующих статьях:

* [*Практические руководства. Управление цифровым двойника*](./how-to-manage-twin.md)
* [*Пошаговое руководство. запрос графа двойника*](./how-to-query-graph.md)