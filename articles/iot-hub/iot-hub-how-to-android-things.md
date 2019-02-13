---
title: Разработка на платформе Android Things с помощью пакетов SDK для Центра Интернета вещей Azure | Документация Майкрософт
description: Руководство для разработчиков. Дополнительные сведения о разработке на платформе Android Things с помощью пакетов SDK для Центра Интернета вещей Azure.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 1/30/2019
ms.author: yizhon
ms.openlocfilehash: b213642b093c3b5f79e5993af91ae51517f09c70
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747914"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Разработка для мобильных устройств с помощью пакетов SDK для Центра Интернета вещей Azure
[Пакеты SDK для Центра Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) обеспечивают поддержку первого уровня для широкого спектра популярных платформ, включая Windows, Linux, OSX, MBED, и мобильных платформ, таких как Android и iOS.  Как часть наших обязательств по расширению выбора и гибкости развертываний в Центре Интернета вещей пакет SDK для Java также поддерживает платформу [Android Things](https://developer.android.com/things/).  Разработчики могут пользоваться преимуществами операционной системы Android Things на стороне устройства, используя [Центр Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) в качестве центрального концентратора сообщений, который масштабируется до миллионов одновременно подключенных устройств. 

В этом руководстве описано, как создать приложение на стороне устройства на платформе Android Things при помощи пакета SDK Java для Центра Интернета вещей Azure.

## <a name="prerequisites"></a>Предварительные требования
* Оборудование, поддерживаемое платформой Android Things, под управлением операционной системы Android Things.  Вы можете выполнить инструкции по установке Android Things, приведенные в [документации по Android Things](https://developer.android.com/things/get-started/kits#flash-at).  Убедитесь, что устройство под управлением Android Things подключено к Интернету и имеются все необходимые периферийные устройства, такие как клавиатура, дисплей и мышь.  В этом руководстве используется Raspberry Pi 3.
* Последняя версия [Android Studio](https://developer.android.com/studio/).
* Последняя версия [Git](https://git-scm.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Регистрация устройства

Устройство должно быть зарегистрировано в Центре Интернета вещей, прежде чем оно сможет подключиться. В этом кратком руководстве для регистрации имитируемого устройства используется Azure Cloud Shell.

1. Выполните приведенные ниже команды в Azure Cloud Shell, чтобы добавить расширение CLI Центра Интернета вещей и создать удостоверение устройства. 

   **YourIoTHubName**. Замените этот заполнитель именем вашего Центра Интернета вещей.

   **MyAndroidThingsDevice**. Это имя, присвоенное зарегистрированному устройству. Используйте MyAndroidThingsDevice, как показано ниже. Если вы выбрали другое имя для устройства, используйте его при работе с этим руководством и обновите имя устройства в примерах приложений перед их запуском.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Выполните следующую команду в Azure Cloud Shell, чтобы получить _строку подключения_ зарегистрированного устройства:  **YourIoTHubName**. Замените этот заполнитель именем вашего Центра Интернета вещей.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Запишите строку подключения устройства, которая выглядит так:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Это значение понадобится позже в рамках этого краткого руководства.

## <a name="building-an-android-things-application"></a>Создание приложения Android Things
1.  Первым шагом в создании приложения Android Things является подключение к устройствам Android Things.  Подключите устройство Android Things к дисплею, а затем настройте подключение к Интернету.  Для Android Things предоставляется [документация](https://developer.android.com/things/get-started/kits) по подключению к Wi-Fi.  После подключения к Интернету запишите IP-адрес, указанный в разделе Networks (Сети).
2.  Подключитесь к устройству Android Things с помощью средства [Android Debug Bridge](https://developer.android.com/studio/command-line/adb) с использованием записанного ранее IP-адреса.  Еще раз проверьте подключение, выполнив следующую команду в окне терминала.  Устройства должны отображаться с состоянием connected (подключено).
    ```
    adb devices
    ```
3.  Скачайте наш пример для Android или Android Things из [репозитория](https://github.com/Azure-Samples/azure-iot-samples-java) или используйте Git.
    ```
    git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
    ```
4.  В Android Studio откройте проект Android, находящийся в папке \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample.
5.  Откройте файл gradle.properties и замените Device_connection_string строкой подключения устройства, записанной ранее.
6.  Щелкните Run — Debug (Запуск — отладка) и выберите устройство Android Things, чтобы развернуть на нем этот код.
7.  После успешного запуска приложения вы увидите, что оно запущено на вашем устройстве Android Things.  Этот пример приложения отправляет случайно сгенерированные показатели температуры.

## <a name="read-the-telemetry-from-your-hub"></a>Чтение данных телеметрии из концентратора

Пример приложения, выполняемого в эмуляторе XCode, показывает данные о сообщениях, отправленных с устройства. Вы также можете просматривать данные в Центре Интернета вещей по мере их получения. Расширение CLI для Центра Интернета вещей позволяет подключить конечную точку сервера **События** к Центру Интернета вещей. Расширение получает сообщения с устройства в облако, отправленные с имитированного устройства. Внутреннее приложение Центра Интернета вещей обычно запускается в облаке, чтобы получать сообщения с устройства в облако и обрабатывать их.

Выполните следующие команды в Azure Cloud Shell, заменив `YourIoTHubName` на имя вашего центра Интернета вещей:

```
azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о том, как управлять подключениями и надежным обменом сообщениями с помощью пакетов SDK центра Интернета вещей, см. в [этой статье](iot-hub-reliability-features-in-sdks.md).
* Дополнительные сведения см. в статье [Разработка для мобильных устройств с помощью пакетов SDK для Центра Интернета вещей Azure](iot-hub-how-to-develop-for-mobile-devices.md).
* [Поддержка платформ пакетов SDK для устройств Azure IoT](iot-hub-device-sdk-platform-support.md)