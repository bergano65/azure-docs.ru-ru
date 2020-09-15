---
title: Автоматическое управление устройствами с помощью DPS
titleSuffix: Azure Digital Twins
description: Узнайте, как настроить автоматизированный процесс подготовки и снятия с учета устройств IoT в Azure Digital двойников с помощью службы подготовки устройств (DPS).
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: efc507cb69b3368a2102b6de0b905657d5806ef2
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561437"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Автоматическое управление устройствами в цифровом двойников Azure с помощью службы подготовки устройств (DPS)

Из этой статьи вы узнаете, как интегрировать Azure Digital двойников со [службой подготовки устройств (DPS)](../iot-dps/about-iot-dps.md).

Решение, описанное в этой статье, позволит автоматизировать процесс **_подготовки_** и **_снятия с учета_** устройств центра Интернета вещей в Azure Digital двойников с помощью службы подготовки устройств. 

Дополнительные сведения о стадиях _подготовки_ и _снятия с учета_ и ознакомлении с набором общих стадий управления устройствами, общих для всех корпоративных проектов IOT, см. в [разделе *жизненный цикл устройства* ](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) документации по управлению устройствами в центре Интернета вещей.

## <a name="prerequisites"></a>предварительные требования

Перед настройкой подготовки необходимо иметь **экземпляр Digital двойников для Azure** , содержащий модели и двойников. Этот экземпляр также должен быть настроен с возможностью обновлять сведения о цифровом двойника на основе данных. 

Если вы еще не настроили этот параметр, вы можете создать его, следуя руководству по цифровому двойников Azure [*: подключение комплексного решения*](tutorial-end-to-end.md). В этом учебнике вы узнаете, как настроить экземпляр Azure Digital двойников с моделями и двойников, подключенным [центром Интернета вещей](../iot-hub/about-iot-hub.md)Azure и несколькими [функциями Azure](../azure-functions/functions-overview.md) для распространения потока данных.

При настройке экземпляра вам потребуются следующие значения, приведенные далее в этой статье. Если вам нужно снова собрать эти значения, используйте приведенные ниже ссылки для получения инструкций.
* **_Имя узла_** экземпляра Azure Digital Twins ([как найти на портале](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* **_Строка подключения_** к концентратору событий Azure ([Поиск на портале](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal))

В этом примере также используется **симулятор устройств** , который включает подготовку с помощью службы подготовки устройств. Симулятор устройства расположен здесь: [пример интеграции Azure Digital двойников и центра Интернета вещей](https://docs.microsoft.com/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Получите пример проекта на своем компьютере, перейдя по ссылке с образцом и нажав кнопку *скачать ZIP-файл* под заголовком. Распакуйте скачанный каталог.

Симулятор устройства основан на **Node.js**версии 10.0. x или более поздней. [*Подготовка среды разработки*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) . описывает, как установить Node.js для этого руководства в Windows или Linux.

## <a name="solution-architecture"></a>Архитектура решения

На рисунке ниже показана архитектура этого решения с использованием Azure Digital двойников со службой подготовки устройств. В нем показаны поток инициализации устройства и снятия с учета.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Представление устройства и нескольких служб Azure в комплексном сценарии. Данные передаются между устройством термостата и службой DPS. Данные также передаются из DPS в центр Интернета вещей и в Azure Digital двойников с помощью функции Azure с меткой "выделение". Данные из действия "Удаление устройства" вручную проходят через центр Интернета вещей, > концентраторы событий > функции Azure > Azure Digital двойников.":::

Эта статья разделена на две части:
* [*Автоматическая подготовка устройства с помощью службы подготовки устройств*](#auto-provision-device-using-device-provisioning-service)
* [*Автоматическое прекращение использования устройства с помощью событий жизненного цикла центра Интернета вещей*](#auto-retire-device-using-iot-hub-lifecycle-events)

Более подробные объяснения каждого шага в архитектуре см. в разделе отдельные разделы этой статьи.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Автоматическая подготовка устройства с помощью службы подготовки устройств

В этом разделе вы будете присоединить службу подготовки устройств к Azure Digital двойников для автоматической подготовки устройств по указанному ниже пути. Это выдержка из полной показанной [выше](#solution-architecture)архитектуры.

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Поток инициализации — это выдержка из схемы архитектуры решения с числами, обозначенными разделами последовательности. Данные передаются между устройством термостата и DP (1 для устройства > DP и 5 для > диагностики). Данные также передаются из DPS в центр Интернета вещей (4) и в Azure Digital двойников (3) с помощью функции Azure с меткой "выделение" (2).":::

Ниже приведено описание потока процесса.
1. Устройство обращается к конечной точке DPS, передавая идентификационные данные для подтверждения своей личности.
2. Служба DPS проверяет удостоверение устройства, проверяя идентификатор регистрации и ключ в списке регистрации и вызывает [функцию Azure](../azure-functions/functions-overview.md) для выделения.
3. Функция Azure создает новый [двойника](concepts-twins-graph.md) в Azure Digital двойников для устройства.
4. DPS регистрирует устройство в центре Интернета вещей и заполняет требуемое состояние двойникаа устройства.
5. Центр Интернета вещей возвращает сведения об ИДЕНТИФИКАТОРе устройства и сведения о подключении центра Интернета вещей на устройстве. Теперь устройство может подключаться к центру Интернета вещей.

В следующих разделах описаны действия по настройке этого потока устройства автоматической инициализации.

### <a name="create-a-device-provisioning-service"></a>Создание службы подготовки устройств

При подготовке нового устройства с помощью службы подготовки устройств в Azure Digital двойников можно создать новый двойника для этого устройства.

Создайте экземпляр службы подготовки устройств, который будет использоваться для подготовки устройств IoT. Можно либо использовать приведенные ниже инструкции Azure CLI, либо использовать портал Azure: Краткое руководство по [*настройке службы подготовки устройств для центра Интернета вещей с помощью портал Azure*](../iot-dps/quick-setup-auto-provision.md).

Следующая команда Azure CLI создаст службу подготовки устройств. Необходимо указать имя, группу ресурсов и регион. Команду можно выполнить в [Cloud Shell](https://shell.azure.com)или локально, если [на компьютере установлен](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)Azure CLI.

```azurecli
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Создание функции Azure

Далее вы создадите функцию, активируемую HTTP-запросом, внутри приложения-функции. Вы можете использовать приложение-функцию, созданное в комплексном учебнике ([*руководство по подключению комплексного решения*](tutorial-end-to-end.md)) или к своему собственному интерфейсу.

Эта функция будет использоваться службой подготовки устройств в [пользовательской политике распределения](../iot-dps/how-to-use-custom-allocation-policies.md) , которая подготавливает новое устройство. Дополнительные сведения об использовании HTTP-запросов с помощью функций Azure см. в статье [*триггер HTTP-запросов Azure для функций Azure*](../azure-functions/functions-bindings-http-webhook-trigger.md).

В проекте приложения-функции добавьте новую функцию. Кроме того, добавьте в проект новый пакет NuGet: `Microsoft.Azure.Devices.Provisioning.Service` .

В созданном файле кода функции вставьте следующий код.

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Devices.Shared;
using Microsoft.Azure.Devices.Provisioning.Service;
using System.Net.Http;
using Azure.Identity;
using Azure.DigitalTwins.Core;
using Azure.Core.Pipeline;
using Azure;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DpsAdtAllocationFunc
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DpsAdtAllocationFunc")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log)
        {
            // Get request body
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogDebug($"Request.Body: {requestBody}");
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Get registration ID of the device
            string regId = data?.deviceRuntimeContext?.registrationId;

            bool fail = false;
            string message = "Uncaught error";
            ResponseObj obj = new ResponseObj();

            // Must have unique registration ID on DPS request 
            if (regId == null)
            {
                message = "Registration ID not provided for the device.";
                log.LogInformation("Registration ID: NULL");
                fail = true;
            }
            else
            {
                string[] hubs = data?.linkedHubs.ToObject<string[]>();

                // Must have hubs selected on the enrollment
                if (hubs == null)
                {
                    message = "No hub group defined for the enrollment.";
                    log.LogInformation("linkedHubs: NULL");
                    fail = true;
                }
                else
                {
                    // Find or create twin based on the provided registration ID and model ID
                    dynamic payloadContext = data?.deviceRuntimeContext?.payload;
                    string dtmi = payloadContext.modelId;
                    log.LogDebug($"payload.modelId: {dtmi}");
                    string dtId = await FindOrCreateTwin(dtmi, regId, log);

                    // Get first linked hub (TODO: select one of the linked hubs based on policy)
                    obj.iotHubHostName = hubs[0];

                    // Specify the initial tags for the device.
                    TwinCollection tags = new TwinCollection();
                    tags["dtmi"] = dtmi;
                    tags["dtId"] = dtId;

                    // Specify the initial desired properties for the device.
                    TwinCollection properties = new TwinCollection();

                    // Add the initial twin state to the response.
                    TwinState twinState = new TwinState(tags, properties);
                    obj.initialTwin = twinState;
                }
            }

            log.LogDebug("Response: " + ((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message));

            return (fail)
                ? new BadRequestObjectResult(message)
                : (ActionResult)new OkObjectResult(obj);
        }

        public static async Task<string> FindOrCreateTwin(string dtmi, string regId, ILogger log)
        {
            // Create Digital Twins client
            var cred = new ManagedIdentityCredential(adtAppId);
            var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

            // Find existing twin with registration ID
            string dtId;
            string query = $"SELECT * FROM DigitalTwins T WHERE $dtId = '{regId}' AND IS_OF_MODEL('{dtmi}')";
            AsyncPageable<string> twins = client.QueryAsync(query);
            
            await foreach (string twinJson in twins)
            {
                // Get DT ID from the Twin
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' with Registration ID '{regId}' found in DT");
                return dtId;
            }

            // Not found, so create new twin
            log.LogInformation($"Twin ID not found, setting DT ID to regID");
            dtId = regId; // use the Registration ID as the DT ID

            // Define the model type for the twin to be created
            Dictionary<string, object> meta = new Dictionary<string, object>()
            {
                { "$model", dtmi }
            };
            // Initialize the twin properties
            Dictionary<string, object> twinProps = new Dictionary<string, object>()
            {
                { "$metadata", meta }
            };
            twinProps.Add("Temperature", 0.0);
            await client.CreateDigitalTwinAsync(dtId, System.Text.Json.JsonSerializer.Serialize<Dictionary<string, object>>(twinProps));
            log.LogInformation($"Twin '{dtId}' created in DT");

            return dtId;
        }
    }

    public class ResponseObj
    {
        public string iotHubHostName { get; set; }
        public TwinState initialTwin { get; set; }
    }
}
```

Сохраните файл, а затем опубликуйте приложение-функцию повторно. Инструкции по публикации приложения-функции см. в разделе [*Публикация приложения*](tutorial-end-to-end.md#publish-the-app) в комплексном руководстве.

### <a name="configure-your-function"></a>Настройка функции

Далее необходимо задать в приложении функции переменные среды, содержащие ссылку на созданный вами экземпляр Azure Digital двойников. Если вы использовали полное руководство ([*руководство по подключению комплексного решения*](tutorial-end-to-end.md)), параметр уже будет настроен.

Добавьте параметр с помощью следующей команды Azure CLI:

```azurecli
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Убедитесь, что разрешения и управляемая роль удостоверения настроены правильно для приложения-функции, как описано в разделе [*Назначение разрешений для приложения-функции*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) в сквозном руководстве.

<!-- 
* Azure AD app registration **_Application (client) ID_** ([find in portal](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))

```azurecli
az functionapp config appsettings set --settings "AdtAppId=<Application (client)" ID> -g <resource group> -n <your App Service (function app) name> 
``` -->

### <a name="create-device-provisioning-enrollment"></a>Создание регистрации устройства

Далее необходимо создать регистрацию в службе подготовки устройств с помощью **пользовательской функции выделения**. Следуйте инструкциям, приведенным в разделах [*Создание регистрации*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) и [*Получение уникальных ключей устройств*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) , посвященных службам подготовки устройств, о настраиваемых политиках распределения.

Во время этого процесса вы свяжете регистрацию с созданной функцией, выбрав функцию на этапе, чтобы **выбрать способ назначения устройств концентраторам**. После создания регистрации имя регистрации и первичный или вторичный ключ SAS будут использоваться позже для настройки симулятора устройства для этой статьи.

### <a name="set-up-the-device-simulator"></a>Настройка симулятора устройств

В этом примере используется симулятор устройства, который включает подготовку с помощью службы подготовки устройств. Симулятор устройства расположен здесь: [пример интеграции Azure Digital двойников и центра Интернета вещей](https://docs.microsoft.com/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Если вы еще не скачали пример, получите его сейчас, перейдя по ссылке образца и нажав кнопку *скачать ZIP-файл* под заголовком. Распакуйте скачанный каталог.

Откройте окно командной строки и перейдите в папку downloaded, а затем в каталог *Device-симулятор* . Установите зависимости для проекта с помощью следующей команды:

```cmd
npm install
```

Затем скопируйте файл *. env. Template* в новый файл с именем *. env*и заполните следующие параметры:

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

Сохраните и закройте файл.

### <a name="start-running-the-device-simulator"></a>Начало работы симулятора устройства

Находясь в каталоге *симулятора устройства* в командном окне, запустите симулятор устройства с помощью следующей команды:

```cmd
node .\adt_custom_register.js
```

Вы должны увидеть зарегистрированное устройство и подключиться к центру Интернета вещей, а затем начать отправку сообщений.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="командное окно отображения регистрации устройства и отправки сообщений":::

### <a name="validate"></a>Проверить

В результате выполнения последовательности, настроенной в этой статье, устройство будет автоматически зарегистрировано в Azure Digital двойников. С помощью следующей команды [интерфейса командной строки Azure Digital двойников](how-to-use-cli.md) найдите двойника устройства в созданном вами экземпляре Azure Digital двойников.

```azurecli
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Вы должны увидеть двойника устройства в экземпляре цифрового двойников Azure.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="командное окно, показывающий только что созданный двойника":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Автоматическое прекращение использования устройства с помощью событий жизненного цикла центра Интернета вещей

В этом разделе вы будете присоединить события жизненного цикла центра Интернета вещей к Azure Digital двойников для автоматического снятия с учета устройств по указанному ниже пути. Это выдержка из полной показанной [выше](#solution-architecture)архитектуры.

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Снятие потока устройства с учета — выдержка из схемы архитектуры решения с числами, обозначенными разделами последовательности. Устройство термостата отображается без подключения к службам Azure на схеме. Данные из действия "Удаление устройства" вручную проходят через центр Интернета вещей (1) > концентраторов событий (2) > функциях Azure > Azure Digital двойников (3).":::

Ниже приведено описание потока процесса.
1. Внешний или ручной процесс инициирует удаление устройства в центре Интернета вещей.
2. Центр Интернета вещей удаляет устройство и создает событие [жизненного цикла устройства](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) , которое будет направляться в [концентратор событий](../event-hubs/event-hubs-about.md).
3. Функция Azure удаляет двойника устройства в Azure Digital двойников.

В следующих разделах описаны действия по настройке этого потока устройств, поддерживающего автоматическое снятие с учета.

### <a name="create-an-event-hub"></a>Создание концентратора событий

Теперь необходимо создать [концентратор событий](../event-hubs/event-hubs-about.md)Azure, который будет использоваться для получения событий жизненного цикла центра Интернета вещей. 

Выполните действия, описанные в кратком руководстве по [*созданию концентратора событий*](../event-hubs/event-hubs-create.md) , используя следующие сведения.
* Если вы используете полное руководство ([*руководство по подключению комплексного решения*](tutorial-end-to-end.md)), вы можете повторно использовать группу ресурсов, созданную для комплексного руководства.
* Назовите концентратор событий *лифецикливентс*или какой-либо другой вариант и запомните созданное пространство имен. Они будут использоваться при настройке функции жизненного цикла и маршрута центра Интернета вещей в следующих разделах.

### <a name="create-an-azure-function"></a>Создание функции Azure

Далее вы создадите функцию, активируемую концентраторами событий, в приложении-функции. Вы можете использовать приложение-функцию, созданное в комплексном учебнике ([*руководство по подключению комплексного решения*](tutorial-end-to-end.md)) или к своему собственному интерфейсу. 

Назовите триггер концентратора событий *лифецикливентс*и подключите триггер концентратора событий к концентратору событий, созданному на предыдущем шаге. Если вы использовали другое имя концентратора событий, измените его в соответствии с именем триггера ниже.

Эта функция будет использовать событие жизненного цикла устройства центра Интернета вещей для снятия существующего устройства с учета. Дополнительные сведения о событиях жизненного цикла см. в статье [*события, не связанные с телеметрии центра Интернета вещей*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Дополнительные сведения об использовании концентраторов событий с функциями Azure см. в статье [*триггер концентраторов событий Azure для функций Azure*](../azure-functions/functions-bindings-event-hubs-trigger.md).

В опубликованном приложении-функции добавьте новый класс функции типа " *триггер концентратора событий*" и вставьте код ниже.

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Azure;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DeleteDeviceInTwinFunc
    {
        private static string adtAppId = "https://digitaltwins.azure.net";
        private static readonly string adtInstanceUrl = System.Environment.GetEnvironmentVariable("ADT_SERVICE_URL", EnvironmentVariableTarget.Process);
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DeleteDeviceInTwinFunc")]
        public static async Task Run(
            [EventHubTrigger("lifecycleevents", Connection = "EVENTHUB_CONNECTIONSTRING")] EventData[] events, ILogger log)
        {
            var exceptions = new List<Exception>();

            foreach (EventData eventData in events)
            {
                try
                {
                    //log.LogDebug($"EventData: {System.Text.Json.JsonSerializer.Serialize(eventData)}");

                    string opType = eventData.Properties["opType"] as string;
                    if (opType == "deleteDeviceIdentity")
                    {
                        string deviceId = eventData.Properties["deviceId"] as string;
                        
                        // Create Digital Twin client
                        var cred = new ManagedIdentityCredential(adtAppId);
                        var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

                        // Find twin based on the original Registration ID
                        string regID = deviceId; // simple mapping
                        string dtId = await GetTwinId(client, regID, log);
                        if (dtId != null)
                        {
                            await DeleteRelationships(client, dtId, log);

                            // Delete twin
                            await client.DeleteDigitalTwinAsync(dtId);
                            log.LogInformation($"Twin '{dtId}' deleted in DT");
                        }
                    }
                }
                catch (Exception e)
                {
                    // We need to keep processing the rest of the batch - capture this exception and continue.
                    exceptions.Add(e);
                }
            }

            if (exceptions.Count > 1)
                throw new AggregateException(exceptions);

            if (exceptions.Count == 1)
                throw exceptions.Single();
        }


        public static async Task<string> GetTwinId(DigitalTwinsClient client, string regId, ILogger log)
        {
            string query = $"SELECT * FROM DigitalTwins T WHERE T.$dtId = '{regId}'";
            AsyncPageable<string> twins = client.QueryAsync(query);
            await foreach (string twinJson in twins)
            {
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                string dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' found in DT");
                return dtId;
            }

            return null;
        }

        public static async Task DeleteRelationships(DigitalTwinsClient client, string dtId, ILogger log)
        {
            var relationshipIds = new List<string>();

            AsyncPageable<string> relationships = client.GetRelationshipsAsync(dtId);
            await foreach (var relationshipJson in relationships)
            {
                BasicRelationship relationship = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relationshipJson);
                relationshipIds.Add(relationship.Id);
            }

            foreach (var relationshipId in relationshipIds)
            {
                client.DeleteRelationship(dtId, relationshipId);
                log.LogInformation($"Twin '{dtId}' relationship '{relationshipId}' deleted in DT");
            }
        }
    }
}
```

Сохраните проект, а затем опубликуйте приложение функции еще раз. Инструкции по публикации приложения-функции см. в разделе [*Публикация приложения*](tutorial-end-to-end.md#publish-the-app) в комплексном руководстве.

### <a name="configure-your-function"></a>Настройка функции

Далее необходимо задать переменные среды в приложении-функции с помощью более ранней версии, содержащей ссылку на созданный вами экземпляр двойников Azure и концентратор событий. Если вы использовали полное руководство ([*руководство по подключению комплексного решения*](./tutorial-end-to-end.md)), первый параметр будет уже настроен.

Добавьте параметр с помощью этой команды Azure CLI. Команду можно выполнить в [Cloud Shell](https://shell.azure.com)или локально, если [на компьютере установлен](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)Azure CLI.

```azurecli
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Далее необходимо настроить переменную среды функции для подключения к только что созданному концентратору событий.

```azurecli
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

Убедитесь, что разрешения и управляемая роль удостоверения настроены правильно для приложения-функции, как описано в разделе [*Назначение разрешений для приложения-функции*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) в сквозном руководстве.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Создание маршрута центра Интернета вещей для событий жизненного цикла

Теперь необходимо настроить маршрут центра Интернета вещей для маршрутизации событий жизненного цикла устройства. В этом случае вы будете отдельно прослушивать события удаления устройства, идентифицируемые `if (opType == "deleteDeviceIdentity")` . Это вызовет удаление элемента Digital двойника, окончательное прекращение использования устройства и его цифровой двойника.

Инструкции по созданию маршрута центра Интернета вещей описаны в этой статье: [*Использование маршрутизации сообщений центра Интернета вещей для отправки сообщений с устройства в облако в другие конечные точки*](../iot-hub/iot-hub-devguide-messages-d2c.md). В разделе *события, не относящиеся к телеметрии* , объясняется, что в качестве источника данных для маршрута можно использовать **события жизненного цикла устройства** .

Для этой настройки необходимо выполнить следующие действия.
1. Создайте настраиваемую конечную точку концентратора событий центра Интернета вещей. Эта конечная точка должна ориентироваться на концентратор событий, созданный в разделе [*Создание концентратора событий*](#create-an-event-hub) .
2. Добавление маршрута *событий жизненного цикла устройства* . Используйте конечную точку, созданную на предыдущем шаге. Можно ограничить события жизненного цикла устройства, чтобы отправлять только события удаления, добавив запрос маршрутизации `opType='deleteDeviceIdentity'` .
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Добавление маршрута":::

Когда вы пройдете этот поток, все будет настроено на снятие устройств с учета.

### <a name="validate"></a>Проверить

Чтобы активировать процесс выхода из эксплуатации, необходимо вручную удалить устройство из центра Интернета вещей.

В [первой половине этой статьи](#auto-provision-device-using-device-provisioning-service)вы создали устройство в центре Интернета вещей и соответствующий цифровой двойника. 

Теперь перейдите в центр Интернета вещей и удалите это устройство (это можно сделать с помощью [команды Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-delete) или в [портал Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs)). 

Устройство будет автоматически удалено из Azure Digital двойников. 

Используйте следующую команду [Azure Digital ДВОЙНИКОВ CLI](how-to-use-cli.md) , чтобы проверить, был ли удален двойника устройства в экземпляре Digital двойников Azure.

```azurecli
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Вы увидите, что двойника устройства больше не может быть найдено в экземпляре Azure Digital двойников.
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="командное окно с двойника не найден":::

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы, созданные в этой статье, больше не нужны, выполните следующие действия, чтобы удалить их.

С помощью Azure Cloud Shell или локального Azure CLI можно удалить все ресурсы Azure в группе ресурсов с помощью команды [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Это приведет к удалению группы ресурсов. экземпляр Azure Digital двойников; центр Интернета вещей и регистрация устройств концентратора; раздел "Сетка событий" и связанные подписки; пространство имен концентраторов событий и оба приложения функций Azure, включая связанные ресурсы, такие как хранилище.

> [!IMPORTANT]
> Удаление группы ресурсов — процесс необратимый. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. 

```azurecli
az group delete --name <your-resource-group>
```
<!-- 
Next, delete the Azure AD app registration you created for your client app with this command:

```azurecli
az ad app delete --id <your-application-ID>
``` -->

Затем удалите папку примера проекта, скачанную с локального компьютера.

## <a name="next-steps"></a>Дальнейшие действия

Цифровые двойников, созданные для устройств, хранятся в виде плоской иерархии в Azure Digital двойников, но их можно расширить с помощью информации о модели и многоуровневой иерархии для Организации. Дополнительные сведения об этой концепции см. в следующих статье:

* [*Основные понятия: Цифровые двойников и двойника Graph*](concepts-twins-graph.md)

Вы можете написать настраиваемую логику для автоматического предоставления этих сведений с помощью модели и данных графа, уже сохраненных в Azure Digital двойников. Дополнительные сведения об управлении, обновлении и извлечении данных из графа двойников см. в следующих статьях:

* [*Практические руководства. Управление цифровым двойника*](how-to-manage-twin.md)
* [*Пошаговое руководство. запрос графа двойника*](how-to-query-graph.md)