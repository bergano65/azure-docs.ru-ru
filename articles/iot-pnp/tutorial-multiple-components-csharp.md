---
title: Подключение примера кода устройства IoT Plug and Play (предварительная версия) компонента C# к Центру Интернета вещей | Документация Майкрософт
description: Создание и запуск примера кода C# устройства IoT Plug and Play (предварительная версия), который использует несколько компонентов и подключается к Центру Интернета вещей. С помощью обозревателя Интернета вещей Azure просматривайте сведения, отправленные устройством в центр.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 67b71399332fb29a277381a8c2806dbe7fb31d85
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552159"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>Руководство по подключению приложения устройства IoT Plug and Play с несколькими компонентами в Windows к Центру Интернета вещей (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

В этом учебнике показано, как создать пример приложения устройства IoT Plug and Play с компонентами и корневым интерфейсом, подключить его к центру Интернета вещей и с помощью обозревателя центра Интернета вещей Azure просмотреть сведения, отправляемые в центр. Пример приложения написан на языке C# и включен в пакет SDK для устройств центра Интернета вещей Azure для C#. Разработчик решения может использовать обозреватель Интернета вещей Azure, чтобы ознакомиться с возможностями устройства IoT Plug and Play, не просматривая код устройства.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

Для выполнения инструкций из этого учебника в ОС Windows установите в локальной среде Windows такое программное обеспечение:

* [Visual Studio (Community, Professional или Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Обозреватель Интернета вещей Azure

Для взаимодействия с примером устройства во второй части этого учебника используется **обозреватель Интернета вещей Azure**. [Скачайте и установите последний выпуск обозревателя Интернета вещей Azure](./howto-use-iot-explorer.md) для вашей операционной системы.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Выполните следующую команду, чтобы получить _строку подключения к Центру Интернета вещей_ для вашего концентратора. Запишите эту строку подключения. Вы будете использовать ее позже при работе с этим учебником.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Вы также можете использовать обозреватель Интернета вещей, чтобы найти строку подключения для центра Интернета вещей.

Выполните указанную ниже команду, чтобы получить _строку подключения устройства_, добавленного в центр. Запишите эту строку подключения. Вы будете использовать ее позже при работе с этим учебником.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Загрузка кода

С помощью этого учебника вы подготовите среду разработки, которую можно использовать для клонирования и сборки пакета SDK для устройств Центра Интернета вещей Azure для C#.

Откройте командную строку в выбранном каталоге. Выполните следующую команду для клонирования репозитория GitHub [пакетов SDK и библиотек Интернета вещей Azure для C#](https://github.com/Azure/azure-iot-sdk-csharp) в это расположение:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-csharp.git
```

## <a name="build-the-code"></a>Сборка кода

Откройте файл решения **azureiot.sln** в Visual Studio 2019 и задайте проект **TemperatureController** в качестве запускаемого проекта. В **Обозревателе решений** этот файл проекта можно найти в разделе **iothub > Примеры > Устройства**.

Теперь можно создать пример в Visual Studio и запустить его в режиме отладки.

## <a name="run-the-device-sample"></a>Запуск примера устройства

Создайте переменную среды с именем **IOTHUB_DEVICE_CONNECTION_STRING**, чтобы сохранить строку подключения устройства, которую вы записали ранее.

Для трассировки выполнения кода в Visual Studio в Windows добавьте точку останова в функцию `main` в файле program.cs.

Теперь устройство готово к получению команд и обновлений свойств и начало отправлять данные телеметрии в концентратор. Продолжите работу примера, после того как выполните следующие действия.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Проверка кода с помощью обозревателя Интернета вещей Azure

После запуска примера клиента устройства используйте обозреватель Интернета вещей Azure, чтобы убедиться, что он работает.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Просмотр кода

В этом примере реализуется устройство контроллера температуры IoT Plug and Play. Модель, которую реализует этот пример, использует [несколько компонентов](concepts-components.md). [Файл модели языка определения Digital Twins (DTDL) для устройства определения температуры](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) определяет телеметрию, свойства и команды, реализуемые устройством.

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

После подключения устройства к центру Интернета вещей код начнет регистрировать обработчики команд. Команда `reboot` указана в корневом интерфейсе. Команда `getMaxMinReport` указана в каждом из двух компонентов термостата:

```csharp
await s_deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, s_deviceClient);
await s_deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1);
await s_deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2);
```

На двух компонентах термостата имеются отдельные обработчики для обновления требуемых свойств:

```csharp
s_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
s_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);
```

Пример кода отправляет данные телеметрии из каждого компонента термостата:

```csharp
await SendTemperatureAsync(Thermostat1);
await SendTemperatureAsync(Thermostat2);
```

Метод `SendTemperature` использует класс `PnpHhelper` для создания сообщений для каждого компонента:

```csharp
Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

Класс `PnpHelper` содержит другие примеры методов, которые можно использовать с многокомпонентной моделью.

Используйте средство обозревателя Интернета вещей Azure, чтобы просматривать данные телеметрии и свойства двух компонентов термостата:

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Многокомпонентное устройство в обозревателе Интернета вещей Azure":::

Кроме того, средство обозревателя Интернета вещей Azure можно использовать для вызова команд в одном из двух компонентов термостата или в корневом интерфейсе.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого учебника вы узнали, как подключить устройство IoT Plug and Play с компонентами к центру Интернета вещей. Дополнительные сведения о моделях устройства IoT Plug and Play см. в статье

> [!div class="nextstepaction"]
> [Руководство для разработчиков IoT Plug and Play (предварительная версия)](concepts-developer-guide.md)
