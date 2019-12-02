---
title: Подключение примера кода устройства IoT Plug and Play (предварительная версия) к Центру Интернета вещей в Linux | Документация Майкрософт
description: Создайте и запустите пример кода устройства IoT Plug and Play (предварительная версия) в Linux, который подключается к Центру Интернета вещей. Используйте Azure CLI для просмотра сведений, отправленных устройством в центр.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 802578c79fa086c74a56db8d47f83ae96d6b0194
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152143"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Краткое руководство. Подключение примера приложения устройства IoT Plug and Play (предварительная версия) в Linux к Центру Интернета вещей (C для Linux)

В этом кратком руководстве показано, как создать пример приложения устройства IoT Plug and Play в Linux, подключить его к Центру Интернета вещей и с помощью Azure CLI просмотреть сведения, отправляемые в центр. Пример приложения написан на языке C и включен в пакет SDK для устройств Azure IoT для C. Разработчик решения может использовать Azure CLI, чтобы ознакомиться с возможностями устройства IoT Plug and Play, не просматривая код устройства.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

В этом кратком руководстве предполагается, что вы используете Ubuntu Linux. Действия, описанные в этом руководстве, были протестированы в Ubuntu 18.04.

Для выполнения инструкций, указанных в этом кратком руководстве, вам необходимо установить на локальный компьютер Linux следующее программное обеспечение:

Установите **GCC**, **Git**, **cmake** и все зависимости с помощью команды `apt-get`.

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Убедитесь, что версия `cmake` выше **2.8.12**, а версия **GCC** выше **4.4.7**.

```sh
cmake --version
gcc --version
```

## <a name="prepare-an-iot-hub"></a>Подготовка Центра Интернета вещей

Для выполнения инструкций, приведенных в этом кратком руководстве, также необходим Центр Интернета вещей Azure в подписке Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. Если у вас нет Центра Интернета вещей, создайте его, выполнив инструкции, указанные в [этой статье](../iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> На этапе общедоступной предварительной версии функции IoT Plug and Play доступны только в центрах Интернета вещей, созданных в таких регионах, как **Центральная часть США**, **Северная Европа** и **Восточная Япония**.

Если вы используете Azure CLI локально, версия `az` должна быть **2.0.73** или выше. В Azure Cloud Shell используется последняя версия. Используйте команду `az --version`, чтобы проверить версию, установленную на компьютере.

Выполните следующую команду, чтобы добавить расширение Интернета вещей Microsoft Azure для Azure CLI в экземпляр Cloud Shell:
```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Для выполнения действий, описанных в этом кратком руководстве, требуется расширение версии **0.8.5** или более поздней версии. Используйте команду `az extension list`, чтобы проверить установленную версию, а также команду `az extension update`, чтобы выполнить обновление, если это необходимо.

Если вы используете интерфейс командной строки локально, выполните следующую команду для входа в подписку Azure.

```bash
az login
```

Если вы используете Azure Cloud Shell, то вы уже вошли автоматически.

Выполните приведенную ниже команду, чтобы создать удостоверение устройства в Центре Интернета вещей. Замените заполнители **YourIoTHubName** и **YourDeviceID** своими значениями _имени Центра Интернета вещей_ и _идентификатора устройства_.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Выполните следующую команду, чтобы получить _строку подключения устройства_ для зарегистрированного устройства (запишите ее для использования в будущем):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

С помощью этого краткого руководства вы подготовите среду разработки, которую можно использовать для клонирования и сборки пакета SDK для устройств Центра Интернета вещей Azure для C.

Откройте командную строку в выбранном каталоге. Выполните следующую команду для клонирования репозитория GitHub [пакетов SDK и библиотек Интернета вещей Azure для С](https://github.com/Azure/azure-iot-sdk-c) в это расположение:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Выполнение этой операции может занять несколько минут.

## <a name="build-the-code"></a>Сборка кода

Используйте пакет SDK для устройств, чтобы выполнить сборку прилагаемого примера кода. Создаваемое приложение имитирует устройство, которое подключается к Центру Интернета вещей. Оно отправляет данные телеметрии и свойства, а также получает команды.

1. Создайте подкаталог `cmake` в корневой папке пакета SDK для устройства и перейдите к этой папке:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Выполните приведенные ниже команды, чтобы собрать пакет SDK для устройства и сгенерированную заглушку кода:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Обновление репозитория моделей

Перед запуском примера добавьте модель возможностей устройства и определения интерфейса в репозиторий моделей компании.

1. Войдите на портал [Microsoft Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) с помощью рабочей или учебной учетной записи Майкрософт или идентификатора партнера Майкрософт, если он у вас есть.

1. Выберите **Company repository** (Корпоративный репозиторий), а затем **Capability models** (Модели возможностей).

1. Выберите **New** (Создать), затем — **Upload** (Передать).

1. Выберите файл `SampleDevice.capabilitymodel.json` в папке `digitaltwin_client/samples` в корневой папке пакета SDK для устройств. Щелкните **Open** (Открыть), а затем — **Save** (Сохранить), чтобы передать файл модели в репозиторий.

1. Выберите **Company repository** (Корпоративный репозиторий), а затем **Interfaces** (Интерфейсы).

1. Выберите **New** (Создать), затем — **Upload** (Передать).

1. Выберите файл `EnvironmentalSensor.interface.json` в папке `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` в корневой папке пакета SDK для устройств. Щелкните **Open** (Открыть), а затем — **Save** (Сохранить), чтобы передать файл интерфейса в репозиторий.

1. Выберите **Company repository** (Корпоративный репозиторий), а затем **Connection strings** (Строки подключения). Запишите первую строку подключения к _репозиторию моделей компании_. Она которая будет использована позже в этом кратком руководстве.

## <a name="run-the-device-sample"></a>Запуск примера устройства

Запустите пример приложения в пакете SDK для имитации устройства IoT Plug and Play, отправляющего данные телеметрии в ваш центр Интернета вещей. Вот как можно запустить пример приложения.

1. Из папки `cmake` перейдите в папку, содержащую исполняемый файл.

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Запустите исполняемый файл.

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Теперь устройство готово к получению команд и обновлений свойств и начало отправлять данные телеметрии в центр. Продолжите работу примера, после того как выполните следующие действия.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Проверка кода с помощью интерфейса командной строки Azure IoT

После запуска примера клиента устройства убедитесь, что он работает, с помощью Azure CLI.

Используйте следующую команду для просмотра данных телеметрии, отправляемых примером устройства. Чтобы увидеть полученные данные телеметрии, может потребоваться подождать одну или две минуты.

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Чтобы просмотреть свойства, отправленные устройством, используйте следующую команду.

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к Центру Интернета вещей. Дополнительные сведения о создании решения, взаимодействующего с устройствами IoT Plug and Play, см. в следующей статье:

> [!div class="nextstepaction"]
> [Практическое руководство. Connect to and interact with an IoT Plug and Play Preview device](howto-develop-solution.md) (Подключение к устройству IoT Plug and Play (предварительная версия) и взаимодействие с ним)
