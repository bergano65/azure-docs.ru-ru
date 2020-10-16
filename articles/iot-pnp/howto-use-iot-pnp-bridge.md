---
title: Как подключить пример моста IoT Plug and Play, работающий в Linux или Windows, в центр Интернета вещей | Документация Майкрософт
description: Создавайте и запускайте мост IoT Plug and Play в Linux или Windows, который подключается к центру Интернета вещей. С помощью обозревателя Интернета вещей Azure просматривайте сведения, отправленные устройством в центр.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6670f654685f8d5cdcaf55d2b1679738a57ecab4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042802"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Как подключить пример моста IoT Plug and Play под управлением Linux или Windows в центр Интернета вещей

В этом пошаговом окне показано, как создать пример адаптера окружающей среды для центра Интернета вещей Plug and Play, подключить его к центру Интернета вещей и использовать средство Azure IoT Explorer для просмотра отправляемых данных телеметрии. Мост IoT Plug and Play написан на языке C и включает пакет SDK для устройств Azure IoT для C. По завершении работы с этим руководством вы сможете запустить мост центра Интернета вещей Plug and Play и просмотреть данные телеметрии отчетов в Azure IoT Explorer: :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="снимок экрана, на котором показана служба Azure IOT Explorer с таблицей данных телеметрии (влажности, температура) из центра Интернета вещей Plug and Play Bridge.":::

## <a name="prerequisites"></a>Предварительные требования

Операции, описанные в этом кратком руководстве, можно выполнить в Linux или Windows. Команды оболочки, приведенные в этом пошаговом руководство, соответствуют соглашению Windows для разделителей пути " `\` ". Если вы используете Linux, обязательно замените эти разделители для " `/` ".

Необходимые условия зависят от операционной системы:

### <a name="linux"></a>Linux

В этом кратком руководстве предполагается, что вы используете Ubuntu Linux. Действия, описанные в этом кратком руководстве, были протестированы в Ubuntu 18.04.

Для выполнения инструкций, указанных в этом кратком руководстве, в ОС Linux необходимо установить указанное ниже программное обеспечение в локальной среде.

Установите **GCC**, **Git**, **cmake** и все необходимые зависимости с помощью команды `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Убедитесь, что версия `cmake` выше **2.8.12**, а версия **GCC** выше **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Для работы с этим кратким руководством в Windows необходимо установить следующее программное обеспечение в локальной среде:

* [Visual Studio (Community, Professional или Enterprise)](https://visualstudio.microsoft.com/downloads/). При [установке](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio обязательно добавьте рабочую нагрузку **Разработка классических приложений на C++** .
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Обозреватель Интернета вещей Azure

Для взаимодействия с примером устройства во второй части этого краткого руководства используется **обозреватель Интернета вещей Azure**. [Скачайте и установите последний выпуск обозревателя Интернета вещей Azure](./howto-use-iot-explorer.md) для вашей операционной системы.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Выполните следующую команду, чтобы получить _строку подключения центра Интернета вещей_ для центра. Запишите эту строку подключения. Вы будете использовать ее позже при работе с этим кратким руководством.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Вы также можете использовать обозреватель Интернета вещей, чтобы найти строку подключения для центра Интернета вещей.

Выполните указанную ниже команду, чтобы получить _строку подключения устройства_, добавленного в центр. Запишите эту строку подключения. Вы будете использовать ее позже при работе с этим кратким руководством.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>Просмотр модели

Вы будете использовать Azure IoT Explorer в последующих шагах, чтобы просмотреть устройство при подключении к центру Интернета вещей. В обозревателе Azure IoT потребуется локальная копия файла модели, соответствующая **идентификатору модели** , который отправляет устройство. Файл модели позволяет обозревателю Интернета вещей отображать данные телеметрии, свойства и команды, реализуемые устройством.

При скачивании кода на приведенном ниже шаге он включает примеры файлов модели в `pnpbridge/docs/schema` папке. Чтобы подготовить обозреватель IoT Azure, выполните следующие действия.

1. Создайте на локальном компьютере папку *models*.
1. Просмотрите [EnvironmentalSensor.js](https://aka.ms/iot-pnp-bridge-env-model) и сохраните JSON файл в папку *Models* .
1. Просмотрите [RootBridgeSampleDevice.js](https://aka.ms/iot-pnp-bridge-root-model) и сохраните JSON файл в папку *Models* .

## <a name="download-the-code"></a>Загрузка кода

Откройте командную строку в выбранном каталоге. Выполните следующую команду, чтобы клонировать репозиторий Azure [IoT Plug and Play Bridge](https://aka.ms/iotplugandplaybridge) в это расположение:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

После клонирования репозитория центра Интернета вещей Plug and Play на компьютер откройте командную строку администратора и перейдите к каталогу клонированного репозитория:

```cmd
cd pnpbridge
git submodule update --init --recursive
```

Выполнение этой операции может занять несколько минут.

>[!NOTE]
> Если при возникновении проблем с обновлением модуля подсистемы клонирования Git возникли проблемы, это известная проблема с путями файлов Windows и Git см. в разделе https://github.com/msysgit/git/pull/110 . Чтобы устранить эту проблему, можно попробовать выполнить следующую команду: `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>Настройка JSON конфигурации

После клонирования репозитория центра Интернета вещей Plug and Play на компьютер перейдите в `pnpbridge/docs/schema` Каталог клонированного репозитория, где можно найти [конфигурацию JSON](https://aka.ms/iot-pnp-bridge-env-config) или `config.json` пример датчика окружающей среды. Дополнительные сведения о файлах конфигурации см. в [документе Основные понятия о bridge Plug and Play для центра Интернета вещей](concepts-iot-pnp-bridge.md).

Для `root-_interface_model_id` поля потребуется скопировать идентификатор модели Plug and Play IOT, определяющий модель для устройства. В нашем примере поисковый запрос будет выглядеть так: `dtmi:com:example:SampleDevice;1`. Измените следующие параметры в разделе **pnp_bridge_parameters** Node в `config.json` файле в папке ":

  Использование строки подключения (Примечание. symmetric_key должен соответствовать ключу SAS в строке подключения):

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 После заполнения `config.json` файла он должен выглядеть следующим образом:

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 После создания моста его необходимо поместить `config.json` в тот же каталог, что и мост, или указать его путь при запуске.

## <a name="build-the-iot-plug-and-play-bridge"></a>Создание моста Plug and Play IoT

Перейдите в папку *пнпбридже* в каталоге репозитория.

Для Windows выполните следующие действия в [Командная строка разработчика для Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs):

```cmd
cd scripts\windows
build.cmd
```

Аналогичным образом для Linux выполните следующие действия:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>В Windows можно открыть решение, созданное с помощью команды CMAK в Visual Studio 2019. Откройте файл проекта *azure_iot_pnp_bridge. sln* в каталоге CMAK и задайте проект *pnpbridge_bin* в качестве запускаемого проекта в решении. Теперь можно создать пример в Visual Studio и запустить его в режиме отладки.

## <a name="start-the-iot-plug-and-play-bridge"></a>Запуск моста Plug and Play IoT

 Запустите пример моста для Plug and Play центра Интернета вещей для датчиков окружающей среды, перейдя в папку *пнпбридже* и выполнив в командной строке следующую команду:

```bash
 cd cmake/pnpbridge_x86/src/adaptors/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adaptors\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Проверка кода с помощью обозревателя Интернета вещей Azure

После запуска примера клиента устройства используйте обозреватель Интернета вещей Azure, чтобы убедиться, что он работает.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к Центру Интернета вещей. Дополнительные сведения о создании решения, взаимодействующего с устройствами IoT Plug and Play, см. в следующей статье:

* [Что такое мост IoT Plug and Play](./concepts-iot-pnp-bridge.md)
* [См. Справочник разработчика GitHub для центра Интернета вещей Plug and Play Bridge](https://aka.ms/iot-pnp-bridge-dev-doc)
* [Мост IoT Plug and Play на GitHub](https://aka.ms/iotplugandplaybridge)