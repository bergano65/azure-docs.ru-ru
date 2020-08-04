---
title: Подключение примера кода устройства C# IoT Plug and Play (предварительная версия) к Центру Интернета вещей | Документация Майкрософт
description: Создайте и запустите пример кода устройства IoT Plug and Play (предварительная версия) в Windows, который подключается к Центру Интернета вещей. С помощью обозревателя Интернета вещей Azure просматривайте сведения, отправленные устройством в центр.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 015e20fa975563fee8ac2d61f9bad1f9f03738ce
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352671"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c"></a>Краткое руководство. Подключение примера приложения устройства IoT Plug and Play (предварительная версия) в Windows к Центру Интернета вещей (C#)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

В этом кратком руководстве показано, как создать пример приложения устройства IoT Plug and Play, подключить его к Центру Интернета вещей и с помощью обозревателя Интернета вещей Azure просмотреть данные телеметрии, которые он отправляет. Пример приложения написан на языке CSharp и включен в пакет SDK для устройств центра Интернета вещей Azure для C#. Разработчик решения может использовать обозреватель Интернета вещей Azure, чтобы ознакомиться с возможностями устройства IoT Plug and Play, не просматривая код устройства.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством в Windows необходимо установить следующее программное обеспечение в локальной среде:

* [Visual Studio (Community, Professional или Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Обозреватель Интернета вещей Azure

Для взаимодействия с примером устройства во второй части этого краткого руководства используется **обозреватель Интернета вещей Azure**. [Скачайте и установите последний выпуск обозревателя Интернета вещей Azure](./howto-use-iot-explorer.md) для вашей операционной системы.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Выполните следующую команду, чтобы получить _строку подключения к Центру Интернета вещей_ для вашего концентратора. Запишите эту строку подключения. Вы будете использовать ее позже при работе с этим кратким руководством.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Вы также можете использовать обозреватель Интернета вещей, чтобы найти строку подключения для центра Интернета вещей.

Выполните указанную ниже команду, чтобы получить _строку подключения устройства_, добавленного в центр. Запишите эту строку подключения. Вы будете использовать ее позже при работе с этим кратким руководством.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Загрузка кода

С помощью этого краткого руководства вы подготовите среду разработки, которую можно использовать для клонирования и сборки пакета SDK для устройств Центра Интернета вещей Azure для C#.

Откройте командную строку в выбранном каталоге. Выполните следующую команду для клонирования репозитория GitHub [пакетов SDK и библиотек Интернета вещей Azure для C#](https://github.com/Azure/azure-iot-sdk-csharp) в это расположение:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-csharp.git
```

## <a name="build-the-code"></a>Сборка кода

Откройте файл проекта *azure-iot-sdk-csharp/iothub/device/samples/PnpDeviceSamples/Thermostat/Thermostat.csproj* в Visual Studio 2019.

Теперь можно создать пример в Visual Studio и запустить его в режиме отладки.

## <a name="run-the-device-sample"></a>Запуск примера устройства

Создайте переменную среды с именем **IOTHUB_DEVICE_CONNECTION_STRING**, чтобы сохранить строку подключения устройства, которую вы записали ранее.

Для трассировки выполнения кода в Visual Studio в Windows добавьте точку останова в функцию `main` в файле program.cs.

Теперь устройство готово к получению команд и обновлений свойств и начало отправлять данные телеметрии в концентратор. Продолжите работу примера, после того как выполните следующие действия.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Проверка кода с помощью обозревателя Интернета вещей Azure

После запуска примера клиента устройства используйте обозреватель Интернета вещей Azure, чтобы убедиться, что он работает.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Просмотр кода

В этом примере используется простой терморегулятор IoT Plug and Play. Модель, которую реализует этот пример, не использует [компоненты](concepts-components.md) IoT Plug and Play. [Файл модели языка определения Digital Twins (DTDL) для терморегулятора](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) определяет телеметрию, свойства и команды, реализуемые устройством.

Код устройства подключается к центру Интернета вещей с помощью стандартного метода `CreateFromConnectionString`. Устройство отправляет идентификатор модели DTDL, которую он реализует в запросе на подключение. Устройство, отправляющее идентификатор модели, — это устройство IoT Plug and Play:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

Идентификатор модели хранится в коде, как показано в следующем фрагменте кода:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Код, который обновляет свойства, обрабатывает команды и отправляет данные телеметрии, идентичен коду устройства, которое не использует соглашения IoT Plug and Play.

В примере используется библиотека JSON для анализа объектов JSON в полезных данных, отправленных из центра Интернета вещей:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к Центру Интернета вещей. Дополнительные сведения о создании решения, взаимодействующего с устройствами IoT Plug and Play, см. в следующей статье:

> [!div class="nextstepaction"]
> [Практическое руководство. Connect to and interact with an IoT Plug and Play Preview device](howto-develop-solution.md) (Подключение к устройству IoT Plug and Play (предварительная версия) и взаимодействие с ним)
