---
title: Подключение примера кода Java устройства IoT Plug and Play к Центру Интернета вещей | Документация Майкрософт
description: Узнайте, как создать и запустить пример кода устройства IoT Plug and Play, который подключается к центру Интернета вещей. С помощью обозревателя Интернета вещей Azure просматривайте сведения, отправленные устройством в центр.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6dfd63f92b8c420a3c5281ee2962b211b8779dd1
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045063"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-windows-to-iot-hub-java"></a>Краткое руководство. Подключение примера приложения устройства IoT Plug and Play в Windows к Центру Интернета вещей (Java)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

В этом кратком руководстве показано, как создать пример приложения устройства IoT Plug and Play, подключить его к Центру Интернета вещей и с помощью обозревателя Интернета вещей Azure просмотреть данные телеметрии, которые он отправляет. Пример приложения написан на языке Java и включен в пакет SDK для устройств центра Интернета вещей Azure для Java. Разработчик решения может использовать обозреватель Интернета вещей Azure, чтобы ознакомиться с возможностями устройства IoT Plug and Play, не просматривая код устройства.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Для работы с этим кратким руководством в Windows необходимо установить следующее программное обеспечение в локальной среде:

* Пакет SDK для Java SE 8. В статье [Долгосрочная поддержка Java для Azure и Azure Stack](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) выберите пункт **Java 8** в разделе **Долгосрочная поддержка**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Загрузка кода

С помощью этого краткого руководства вы подготовите среду разработки, которую можно использовать для клонирования и сборки пакета SDK для устройств Центра Интернета вещей Azure для Java.

Откройте командную строку в выбранном каталоге. Выполните следующую команду для клонирования репозитория GitHub [пакетов SDK и библиотек Интернета вещей Azure для Java](https://github.com/Azure/azure-iot-sdk-java) в это расположение:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>Сборка кода

В Windows перейдите к корневой папке клонированного репозитория пакета SDK для Java.

Запустите сборку примера приложения с помощью следующей команды.

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Запуск примера устройства

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Дополнительные сведения о примере конфигурации см. в [образце файла сведений](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md).

Перейдите к папке *\device\iot-device-samples\pnp-device-sample\thermostat-device-sample*.

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

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к Центру Интернета вещей. Дополнительные сведения о создании решения, взаимодействующего с устройствами IoT Plug and Play, см. в следующей статье:

> [!div class="nextstepaction"]
> [Практическое руководство. Connect to and interact with an IoT Plug and Play Preview device](./quickstart-service-node.md) (Подключение к устройству IoT Plug and Play (предварительная версия) и взаимодействие с ним)