---
title: Подключение примера кода устройства Java IoT Plug and Play (предварительная версия) к Центру Интернета вещей | Документация Майкрософт
description: Создайте и запустите пример кода устройства IoT Plug and Play (предварительная версия), который подключается к Центру Интернета вещей. С помощью обозревателя Интернета вещей Azure просматривайте сведения, отправленные устройством в центр.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b89c92e675ab505878f350e9716af95050ce28b2
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352676"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-java"></a>Краткое руководство. Подключение примера приложения устройства IoT Plug and Play (предварительная версия) в Windows к Центру Интернета вещей (Java)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

В этом кратком руководстве показано, как создать пример приложения устройства IoT Plug and Play, подключить его к Центру Интернета вещей и с помощью обозревателя Интернета вещей Azure просмотреть данные телеметрии, которые он отправляет. Пример приложения написан на языке Java и включен в пакет SDK для устройств центра Интернета вещей Azure для Java. Разработчик решения может использовать обозреватель Интернета вещей Azure, чтобы ознакомиться с возможностями устройства IoT Plug and Play, не просматривая код устройства.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством в Windows необходимо установить следующее программное обеспечение в локальной среде:

* Пакет SDK для Java SE 8. В статье [Долгосрочная поддержка Java для Azure и Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable) выберите пункт **Java 8** в разделе **Долгосрочная поддержка**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

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

## <a name="download-the-code"></a>Загрузка кода

С помощью этого краткого руководства вы подготовите среду разработки, которую можно использовать для клонирования и сборки пакета SDK для устройств Центра Интернета вещей Azure для Java.

Откройте командную строку в выбранном каталоге. Выполните следующую команду для клонирования репозитория GitHub [пакетов SDK и библиотек Интернета вещей Azure для Java](https://github.com/Azure/azure-iot-sdk-java) в это расположение:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="build-the-code"></a>Сборка кода

В Windows перейдите к корневой папке клонированного репозитория пакета SDK для Java. Затем перейдите к папке *\device\iot-device-samples\pnp-device-sample\thermostat-device-sample*.

Запустите сборку примера приложения с помощью следующей команды.

```cmd
mvn clean package
```

## <a name="run-the-device-sample"></a>Запуск примера устройства

Создайте переменную среды с именем **IOTHUB_DEVICE_CONNECTION_STRING**, чтобы сохранить строку подключения устройства, которую вы записали ранее.

Чтобы запустить пример приложения, выполните следующую команду:

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

Теперь устройство готово к получению команд и обновлений свойств и начало отправлять данные телеметрии в концентратор. Продолжите работу примера, после того как выполните следующие действия.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Проверка кода с помощью обозревателя Интернета вещей Azure

После запуска примера клиента устройства используйте обозреватель Интернета вещей Azure, чтобы убедиться, что он работает.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Просмотр кода

В этом примере используется простой терморегулятор IoT Plug and Play. Модель, которую реализует этот пример, не использует [компоненты](concepts-components.md) IoT Plug and Play. [Файл модели DTDL для терморегулятора](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) определяет данные телеметрии, свойства и команды, выполняемые устройством.

Код устройства использует стандартный класс `DeviceClient` для подключения к центру Интернета вещей. Устройство отправляет идентификатор модели DTDL, которую он реализует в запросе на подключение. Устройство, отправляющее идентификатор модели, — это устройство IoT Plug and Play:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

Идентификатор модели хранится в коде, как показано в следующем фрагменте кода:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Код, который обновляет свойства, обрабатывает команды и отправляет данные телеметрии, идентичен коду устройства, которое не использует соглашения IoT Plug and Play.

В примере используется библиотека JSON для анализа объектов JSON в полезных данных, отправленных из центра Интернета вещей:

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к Центру Интернета вещей. Дополнительные сведения о создании решения, взаимодействующего с устройствами IoT Plug and Play, см. в следующей статье:

> [!div class="nextstepaction"]
> [Практическое руководство. Connect to and interact with an IoT Plug and Play Preview device](howto-develop-solution.md) (Подключение к устройству IoT Plug and Play (предварительная версия) и взаимодействие с ним)
