---
title: Подключение кода IoT Plug and Play (предварительная версия) к Центру Интернета вещей на Java | Документация Майкрософт
description: С помощью Java создайте и запустите пример кода устройства IoT Plug and Play (предварительная версия), который подключается к Центру Интернета вещей. С помощью обозревателя Интернета вещей Azure просматривайте сведения, отправленные устройством в центр.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c85763ffb12ac93f3aff667c4660afa22ca6a99f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86521264"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>Краткое руководство. Подключение примера приложения IoT Plug and Play (предварительная версия) к Центру Интернета вещей (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

В этом кратком руководстве показано, как создать пример приложения устройства IoT Plug and Play, подключить его к Центру Интернета вещей и с помощью обозревателя IoT Azure просмотреть сведения, отправляемые в Центр. Пример приложения написан на языке Java и входит в состав коллекции примеров Azure IoT для Java. Разработчик решения может использовать обозреватель Интернета вещей Azure, чтобы ознакомиться с возможностями устройства IoT Plug and Play, не просматривая код устройства.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

Для выполнения инструкций, приведенных в этом кратком руководстве, необходимо установить платформу Java SE 8 на компьютере для разработки. Кроме того, необходимо установить Maven 3.

Подробные сведения см. в статье о [подготовке среды разработки](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) в пакете SDK для устройств Azure IoT для Java.

### <a name="install-the-azure-iot-explorer"></a>Установка обозревателя Azure IoT

Скачайте и установите последний выпуск **обозревателя Azure IoT** со страницы [репозитория](https://github.com/Azure/azure-iot-explorer/releases) инструментов, выбрав MSI-файл в разделе "Assets" (Ресурсы) для последнего обновления.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Выполните следующую команду, чтобы получить _строку подключения к вашему Центру Интернета вещей_ (запишите для использования в будущем):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

С помощью сведений, приведенных в этом кратком руководстве, вы подготовите среду разработки, которую можно использовать для клонирования и создания примеров Azure IoT для Java.

Откройте окно терминала в предпочитаемом каталоге. Выполните следующую команду для клонирования репозитория [примеров Azure IoT для Java](https://github.com/Azure-Samples/azure-iot-samples-java) с GitHub в это расположение:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

Выполнение этой операции займет несколько минут.

## <a name="build-the-code"></a>Сборка кода

Вы используете клонированный пример кода для создания приложения, которое моделирует устройство, подключаемое к Центру Интернета вещей. Оно отправляет данные телеметрии и свойства, а также получает команды.

1. В окне локального терминала перейдите в папку клонированного репозитория, а затем в папку **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample**. Затем установите необходимые библиотеки и создайте приложение имитированного устройства, выполнив следующую команду:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Настройте _строку подключения к устройству_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>Запуск примера устройства

Запустите пример приложения для имитации устройства IoT Plug and Play, отправляющего данные телеметрии в ваш Центр Интернета вещей. Чтобы запустить пример приложения, используйте следующую команду:

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

Отобразятся сообщения о том, что устройство подключено, выполняет различные шаги по настройке и ожидает обновления службы. После этого отобразятся журналы телеметрии. Это указывает на то, что устройство готово к получению команд и обновлений свойств и начало отправлять данные телеметрии в центр. Продолжите работу примера, после того как выполните следующие действия.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Проверка кода с помощью обозревателя Azure IoT

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Чтобы убедиться, что средство может считывать определения моделей интерфейсов с устройства, выберите **Параметры**. В меню "Параметры" **On the connected device** (На подключенном устройстве) может уже отображаться в конфигурациях Plug and Play. Если это не так, выберите **+ Add module definition source** (+ Добавить источник определения модуля), а затем **On the connected device** (На подключенном устройстве), чтобы добавить его.

1. На странице обзора **устройства** найдите созданное ранее удостоверение устройства. Если приложение устройства по-прежнему выполняется в командной строке, убедитесь, что **состояние подключения устройства** в обозревателе Интернета вещей Azure — _Подключено_ (в противном случае нажимайте кнопку **Refresh** (Обновить), пока это состояние не появится). Выберите устройство, чтобы просмотреть дополнительные сведения.

1. Разверните интерфейс, указав идентификатор **urn:ПРИМЕР_ПАКЕТА_SDK_java:EnvironmentalSensor:1**, чтобы увидеть интерфейс и примитивы IoT Plug and Play: свойства, команды и данные телеметрии.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к Центру Интернета вещей. Дополнительные сведения о создании решения, взаимодействующего с устройствами IoT Plug and Play, см. в следующей статье:

> [!div class="nextstepaction"]
> [Практическое руководство. Подключение к устройству IoT Plug and Play (предварительная версия) и взаимодействие с ним](howto-develop-solution.md)
