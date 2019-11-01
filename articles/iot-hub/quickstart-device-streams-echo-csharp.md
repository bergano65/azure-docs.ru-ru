---
title: Обмен данными с приложением устройства с помощью C# и потоков устройств Центра Интернета вещей (предварительная версия) | Документация Майкрософт
description: В этом кратком руководстве выполняются два примера приложений C#, взаимодействующих через поток устройств, установленный при помощи центра Интернета вещей.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c994b77105fe94eef418c0befc4c135ec09ada14
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900929"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Краткое руководство. Обмен данными с приложением устройства с помощью C# и потоков устройств Центра Интернета вещей (предварительная версия)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Центр Интернета вещей Azure поддерживает потоки устройств, которые сейчас доступны в режиме [предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Потоки устройств Центра Интернета вещей](./iot-hub-device-streams-overview.md) позволяют службам и приложениям устройств безопасным и подходящим методом обмениваться данными с брандмауэром. Это краткое руководство включает в себя две программы C#, использующие преимущества потоков устройств для обмена данными (вывод на экран).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Предварительная версия потоков устройств сейчас поддерживается только в центрах Интернета вещей, созданных в следующих регионах:
  * Центральный регион США
  * Центральная часть США (EUAP)
  * Северная Европа
  * Юго-Восточная Азия

* Два примера приложений, запускаемые в рамках этого краткого руководства, написаны на языке C#. На компьютере, на котором ведется разработка, необходимо установить пакет SDK для .NET Core версии 2.1.0 или более поздней.
  * Скачайте [пакет SDK для .NET Core с поддержкой нескольких платформ](https://www.microsoft.com/net/download/all).
  * Проверьте текущую версию C# на компьютере, на котором ведется разработка, используя следующую команду:

   ```
   dotnet --version
   ```

* Чтобы добавить расширение Интернета вещей Azure для Azure CLI в экземпляр Cloud Shell, выполните приведенную ниже команду. Расширение Интернета вещей добавляет в Azure CLI специальные команды Центра Интернета вещей, IoT Edge и Службы подготовки устройств к добавлению в центр Интернета вещей (DPS).

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Скачайте пример проекта C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) по ссылке и извлеките ZIP-архив. Он понадобится на стороне устройства и на стороне службы.

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Регистрация устройства

Устройство должно быть зарегистрировано в Центре Интернета вещей, прежде чем оно сможет подключиться. При изучении этого раздела для регистрации имитированного устройства используется Azure Cloud Shell.

1. Чтобы создать удостоверение устройства, выполните приведенные ниже команды в Cloud Shell.

   > [!NOTE]
   > * Замените заполнитель *YourIoTHubName* именем созданного центра Интернета вещей.
   > * Для имени регистрируемого устройства рекомендуется использовать имя *MyDevice*, как показано в примере. Если вы выбрали другое имя для устройства, используйте его при работе с этой статьей и обновите имя устройства в примерах приложений перед их запуском.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Выполните следующую команду в Azure Cloud Shell, чтобы получить *строку подключения* зарегистрированного устройства.

   > [!NOTE]
   > Замените заполнитель *YourIoTHubName* именем созданного центра Интернета вещей.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Запишите возвращенную строку подключения к устройству для последующего использования в этом кратком руководстве. Это должно выглядеть следующим образом:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Понадобится также *строка подключения к службе* из Центра Интернета вещей, чтобы включить приложение на стороне службы для подключения к Центру Интернета вещей и установить потоки устройств. Следующая команда получает это значение для Центра Интернета вещей:

   > [!NOTE]
   > Замените заполнитель *YourIoTHubName* именем созданного центра Интернета вещей.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Запишите возвращенную строку подключения к службе для последующего использования в этом кратком руководстве. Это должно выглядеть следующим образом:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Обмен данными между устройством и службой через потоки устройств

В рамках этого раздела вы запустите приложения на стороне устройства и на стороне службы, а также настроите обмен данными между двумя этими приложениями.

### <a name="run-the-service-side-application"></a>Запуск приложения на стороне службы

В окне терминала на локальном компьютере перейдите к каталогу `iot-hub/Quickstarts/device-streams-echo/service` в распакованной папке проекта. Держите следующие сведения под рукой:

| Имя параметра | Значение параметра |
|----------------|-----------------|
| `ServiceConnectionString` | Строка подключения к службе центра Интернета вещей. |
| `MyDevice` | Идентификатор устройства, созданного ранее. |

Выполните компиляцию и запуск кода с помощью следующих команд:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
Приложение будет ожидать, пока приложение устройства станет доступным.

> [!NOTE]
> Происходит превышение времени ожидания, если приложения на стороне устройства не отвечает вовремя.

### <a name="run-the-device-side-application"></a>Запуск приложения на стороне устройства

В другом окне терминала на локальном компьютере перейдите к каталогу `iot-hub/Quickstarts/device-streams-echo/device` в распакованной папке проекта. Держите следующие сведения под рукой:

| Имя параметра | Значение параметра |
|----------------|-----------------|
| `DeviceConnectionString` | Строка подключения к устройству Центра Интернета вещей. |

Выполните компиляцию и запуск кода с помощью следующих команд:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

В конце последнего шага приложение на стороне службы инициирует поток на ваше устройство. После установления потока приложение отправляет строковый буфер в службу через поток. В этом примере приложение на стороне службы просто возвращает те же данные устройству, демонстрируя успешную двустороннюю связь между двумя приложениями.

Выходные данные консоли на стороне устройства:

![Выходные данные консоли на стороне устройства](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Выходные данные консоли на стороне службы.

![Выходные данные консоли на стороне службы](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Трафик, передаваемый по потоку, туннелируется через центр Интернета вещей, а не напрямую. См. подробнее о [преимуществах потоков устройств](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы настроили центр Интернета вещей, зарегистрировали устройство, установили поток устройств между приложением C# на стороне устройства и службы и использовали поток для обмена данными между приложениями.

Дополнительные сведения о потоках устройств см. в следующей статье:

> [!div class="nextstepaction"]
> [IoT Hub Device Streams (preview)](./iot-hub-device-streams-overview.md) (Потоки устройств (предварительная версия))
