---
title: Взаимодействие с устройством IoT Plug and Play, подключенным к решению Интернета вещей Azure (C#) | Документация Майкрософт
description: Подключение к устройству IoT Plug and Play, подключенному к решению Интернета вещей Azure, а также взаимодействие с ним с помощью C#.
author: ericmitt
ms.author: ericmitt
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b35268cd8d36901f750225713407c5392e5c429e
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91759175"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-c"></a>Краткое руководство. Взаимодействие с подключенным к решению устройством IoT Plug and Play с помощью C#

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug and Play упрощает работу с Интернетом вещей, позволяя взаимодействовать с возможностями устройства без знаний базовой реализации устройства. В этом кратком руководстве показано, как подключиться к устройству IoT Plug and Play, подключенному к решению, а также управлять им с помощью C#.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Для работы с этим кратким руководством в Windows необходимо установить следующее ПО на компьютер разработки:

* [Visual Studio (Community, Professional или Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Клонирование репозитория пакета SDK с помощью примера кода

Если вы ознакомились с [Кратким руководством по подключению примера приложения устройства IoT Plug and Play в Windows к Центру Интернета вещей (C#)](quickstart-connect-device-csharp.md), значит вы уже клонировали репозиторий.

Клонируйте примеры из репозитория пакета SDK для Интернета вещей Microsoft Azure для .NET на сайте GitHub. Откройте командную строку в выбранной папке. С помощью следующей команды клонируйте репозиторий примеров [Интернета вещей Microsoft Azure для .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp) с сайта GitHub.

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Запуск примера устройства

С помощью этого краткого руководства вы сможете использовать пример термостата, написанный на C#, как устройство IoT Plug and Play. Чтобы запустить пример устройства, сделайте следующее:

1. Откройте файл проекта *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* в Visual Studio 2019.

1. В Visual Studio последовательно выберите **Проект > Thermostat Properties (Свойства термостата) > Отладка**. Затем добавьте в проект следующие переменные среды:

    | Имя | Значение |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Значение, которое записали после завершения [настройки среды](set-up-environment.md). |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Значение, которое записали после завершения [настройки среды](set-up-environment.md). |


1. Теперь можно создать пример в Visual Studio и запустить его в режиме отладки.

1. Отобразится сообщение о том, что устройство отправило определенные сведения и находится в сети. Эти сообщения означают, что устройство начало отправлять в концентратор данные телеметрии и теперь готово получать команды и обновления свойств. Не закрывайте этот экземпляр Visual Studio, он понадобится вам, чтобы проверить работу примера службы.

## <a name="run-the-sample-solution"></a>Запуск примера решения

Во время прохождения статьи [Настройка среды для кратких руководств и учебников IoT Plug and Play](set-up-environment.md) вы создали две переменные среды, чтобы настроить пример для подключения к центру Интернета вещей и устройству:

* **IOTHUB_CONNECTION_STRING**: строка подключения центра Интернета вещей, которую вы записали ранее.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

В рамках этого краткого руководства вы примените пример решения Интернета вещей на C# для взаимодействия с только что настроенным примером устройства.

1. В другом экземпляре Visual Studio откройте проект *azure-iot-samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj*.

1. В Visual Studio последовательно выберите **Проект > Thermostat Properties (Свойства термостата) > Отладка**. Затем добавьте в проект следующие переменные среды:

    | Имя | Значение |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-pnp-device |
    | IOTHUB_CONNECTION_STRING | Значение, которое записали после завершения [настройки среды](set-up-environment.md). |

1. Теперь можно создать пример в Visual Studio и запустить его в режиме отладки.

### <a name="get-digital-twin"></a>Получение цифрового двойника

В следующем фрагменте кода показано, как приложение службы получает цифровой двойник:

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> В этом образце используется пространство имен **Microsoft.Azure.Devices.Client;** из **клиента службы Центра Интернета вещей**. Дополнительные сведения об извлечении идентификатора модели см. в [руководстве разработчика](concepts-developer-guide-service.md).

Этот код создаст следующие выходные данные:

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

В следующем фрагменте кода показано, как использовать *исправления* для обновления свойств с помощью цифрового двойника:

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

Этот код создает следующие выходные данные на устройстве при обновлении службой свойства `targetTemperature`:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>Вызов команды

В следующем фрагменте кода показано, как вызвать команду:

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

Этот код создает следующие выходные данные на устройстве при вызове службой команды `getMaxMinReport`:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к решению Интернета вещей. Дополнительные сведения о моделях устройства IoT Plug and Play см. в статье

> [!div class="nextstepaction"]
> [Руководство разработчика IoT Plug and Play](concepts-developer-guide-device-csharp.md)
