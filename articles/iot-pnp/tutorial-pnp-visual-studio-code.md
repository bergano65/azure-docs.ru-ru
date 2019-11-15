---
title: Создание и тестирование устройства IoT Plug and Play (предварительная версия) | Документация Майкрософт
description: Информация для разработчиков устройств об использовании VS Code для создания и тестирования новой модели возможностей устройства, созданной для устройств с предварительной версией IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: b7b9cd1040accda4d39af4d0a18940b56a45f929
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569887"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Руководство по созданию и тестированию модели возможностей устройства с помощью Visual Studio Code

В этом учебнике вы узнаете, как разработчик устройств может использовать Visual Studio Code для создания _модели возможностей устройства_. Эта модель поможет вам создать каркас кода для выполнения на устройстве, которое подключается к облачному экземпляру Центра Интернета вещей Azure.

В разделе этого руководства, посвященном созданию каркаса кода, предполагается работа с ОС Windows.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание модели возможностей устройства;
> * создание каркаса кода устройства на основе модели;
> * реализация заглушек в созданном коде;
> * выполнение кода для проверки взаимодействия с центром Интернета вещей.

## <a name="prerequisites"></a>Предварительные требования

Для работы с моделью возможностей устройства в этом учебнике вам потребуется следующее.

* [Visual Studio Code](https://code.visualstudio.com/download). VS Code предоставляется для нескольких платформ.
* Пакет расширения [Azure IoT Tools для VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools). Выполните следующие действия, чтобы установить пакет расширения в VS Code:

    1. В VS Code выберите вкладку **Расширения**.
    1. Выполните поиск **Azure IoT Tools**.
    1. Щелкните **Установить**.

Для создания кода на языке C для этого учебника в среде ОС Windows вам потребуется следующее.

* [Средства сборки для Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) с рабочими нагрузками **средств сборки C++** и **компонентов диспетчера пакетов NuGet**. Допускается также [Visual Studio (Community, Professional или Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 или 2015 с теми же рабочими нагрузками.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/).

Для проверки кода устройства в этом руководстве вам потребуется следующее.

* [Обозреватель Azure IoT](https://github.com/Azure/azure-iot-explorer/releases).
* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Моделирование устройства

Для создания модели возможностей устройства используется _язык определения цифрового двойника_. Эта модель обычно состоит из нескольких файлов определения _интерфейсов_ и одного файла модели. **Azure IoT Tools для VS Code** включает в себя средства, которые упрощают создание и редактирование таких JSON-файлов.

### <a name="create-the-interface-file"></a>Создание файла интерфейса

Чтобы создать в VS Code файл интерфейса, который определяет возможности устройства IoT, выполните следующее.

1. Создайте папку с именем **devicemodel**.

1. Запустите VS Code и нажмите сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд.

1. Введите строку **Plug and Play**и выберите команду **IoT Plug and Play: Create Interface** (Создать интерфейс IoT Plug and Play).

1. Найдите и выберите папку **devicemodel**, которую вы только что создали.

1. Затем введите для интерфейса имя **EnvironmentalSensor** и нажмите клавишу **ВВОД**. VS Code создаст типовой файл интерфейса с именем **EnvironmentalSensor.interface.json**.

1. Замените содержимое этого файла следующим кодом JSON и замените в нем `{your name}` в поле `@id` уникальным значением. Используйте только символы a–z, A–Z, 0–9 и знак подчеркивания. Дополнительные сведения см. в статье [о формате идентификаторов цифровых двойников](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). Для сохранения интерфейса в репозитории он должен иметь уникальный идентификатор.

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Этот интерфейс определяет свойства устройства, например **Customer Name** (Имя клиента), типы телеметрии, например **Temperature** (Температура), и команды, например **turnon** (Включить).

1. В конец этого файла интерфейса добавьте команду с именем **blink**. Перед командой обязательно добавьте запятую. Попробуйте ввести определение и убедитесь, что функции IntelliSense, автозаполнения и проверки помогают вам редактировать определение интерфейса:

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Сохраните файл.

### <a name="create-the-model-file"></a>Создание файла модели

В файле модели определяются интерфейсы, которые реализует устройство IoT Plug and Play. Обычно в модели существует не менее двух интерфейсов: стандартный интерфейс, обязательный для всех устройств IoT Plug and Play, и один или несколько интерфейсов для конкретных возможностей устройства.

Чтобы создать в VS Code файл модели с определением интерфейсов, которые реализует устройство IoT Plug and Play, выполните следующее.

1. Нажмите сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд.

1. Введите строку **Plug and Play**и выберите команду **IoT Plug and Play: Create Capability Model** (Создание модели возможностей IoT Plug and Play). Затем введите для модели имя **SensorboxModel**. VS Code создаст типовой файл интерфейса с именем **SensorboxModel.capabilitymodel.json**.

1. Замените содержимое этого файла следующим кодом JSON и замените значение `{your name}` в поле `@id` и в интерфейсе `EnvironmentalSensor` тем же значением, которое вы указали в файле **EnvironmentalSensor.interface.json**. Для сохранения интерфейса в репозитории он должен иметь уникальный идентификатор.

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
    }
    ```

    Эта модель определяет устройство, реализующее созданный вами интерфейс **EnvironmentalSensor** и стандартный интерфейс **DeviceInformation**.

1. Сохраните файл.

### <a name="download-the-deviceinformation-interface"></a>Скачивание интерфейса DeviceInformation

Прежде чем создать скелет кода из этой модели, следует создать локальную копию интерфейса **DeviceInformation** из *общедоступного репозитория моделей*. Общедоступный репозиторий моделей уже содержит готовый интерфейс **DeviceInformation**.

Чтобы скачать интерфейс **DeviceInformation** из общедоступного репозитория моделей с помощью VS Code, выполните следующее.

1. Нажмите сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд.

1. Введите **Plug and Play**, а затем выберите команду **Open Model Repository** (Открыть репозиторий моделей) и вариант **Open Public Model Repository** (Открыть общедоступный репозиторий моделей).

1. Выберите **Interfaces** (Интерфейсы), затем выберите интерфейс сведений об устройстве с идентификатором `urn:azureiot:DeviceManagement:DeviceInformation:1` и щелкните **Скачать**.

Теперь в вашу модель возможностей устройства входят три файла:

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>Публикация модели

Чтобы обозреватель Интернета вещей Azure мог считать вашу модель возможностей устройства, ее нужно опубликовать в корпоративном репозитории. Для публикации из VS Code получите строку подключения к корпоративному репозиторию, как описано ниже.

1. Войдите на [портал Azure Certified for IoT](https://aka.ms/ACFI).

1. Для входа используйте данные _рабочей учетной записи_ Майкрософт.

1. Выберите **Company repository** (Корпоративный репозиторий), а затем **Connection strings** (Строки подключения).

1. Скопируйте строку подключения.

Чтобы открыть корпоративный репозиторий в VS Code, выполните следующее.

1. Нажмите сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд.

1. Введите строку **Plug and Play**и выберите команду **IoT Plug and Play: Open Model Repository** (Открыть репозиторий моделей IoT Plug and Play).

1. Выберите **Open Organizational Model Repository** (Открыть репозиторий моделей организации) и вставьте полученную строку подключения.

1. Нажмите клавишу **ВВОД**, чтобы открыть корпоративный репозиторий.

Чтобы опубликовать модель возможностей устройства и интерфейсы в корпоративном репозитории, выполните следующие действия.

1. Нажмите сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд.

1. Введите строку **Plug and Play**и выберите команду **IoT Plug and Play: Submit files to Model Repository** (Отправка файлов в репозиторий моделей IoT Plug and Play).

1. Выберите файлы **EnvironmentalSensor.interface.json** и **SensorboxModel.capabilitymodel.json**, затем нажмите **OK**.

Выбранные файлы сохраняются в корпоративном репозитории.

## <a name="generate-code"></a>Создание кода

Вы можете использовать **Azure IoT Tools для VS Code**, чтобы создать на основе модели схему кода на языке C. Для создания каркаса кода C в VS Code выполните следующее.

1. Нажмите сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд.

1. Введите строку **Plug and Play**и выберите команду **IoT Plug and Play: Generate Device Code Stub** (Создать заглушку кода устройства IoT Plug and Play).

1. Выберите файл модели возможностей **SensorboxModel.capabilitymodel.json**.

1. Введите для проекта имя **sensorbox_app**.

1. Выберите язык **ANSI C**.

1. Выберите способ подключения **Via IoT Hub device connection string** (Через строку подключения к устройству Центра Интернета вещей).

1. Выберите **Проект CMake в Windows** в качестве шаблона проекта.

1. Выберите **Через Vcpkg** как способ включения пакета SDK для устройства.

VS Code создаст каркас кода на языке C и сохранит полученные файлы в папке **sensorbox_app** в папке **modelcode**. Затем VS Code откроет новое окно с созданным кодом.

## <a name="update-the-generated-code"></a>Обновление созданного кода

Прежде чем компилировать и запускать полученный код, вам нужно реализовать все свойства, данные телеметрии и команды, для которых пока существуют только заглушки.

Чтобы создать в VS Code реализацию, заменяющую заглушки, выполните следующее.

1. Откройте файл **SensorboxModel_impl.c**.

1. Добавьте в него код, реализующий нужные функции.

1. Сохраните изменения.

## <a name="build-the-code"></a>Сборка кода

Прежде чем запускать код для проверки устройства IoT Plug and Play в Центре Интернета вещей Azure, необходимо скомпилировать этот код.

Следуйте инструкциям в файле **Readme.md**, который находится в папке **sensorbox_app**, чтобы создать и запустить код в Windows. В следующем разделе приведены инструкции по извлечению строки подключения устройства, которая потребуется при запуске кода устройства.

## <a name="test-the-code"></a>Тестирование кода

При выполнении этот код подключается к Центру Интернета вещей и начинает отправлять тестовые значения телеметрии и значений. Устройство также реагирует на команды, отправленные из Центра Интернета вещей. Чтобы проверить это поведение, выполните:

1. Чтобы создать Центр Интернета вещей, выполните:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Чтобы подключить устройство к Центру Интернета вещей и получить строку подключения для него, выполните:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Запишите строку подключения.

1. В командной строке перейдите в папку **azure-iot-sdk-c**, в которой вы скомпилировали пакет SDK и примеры кода. Затем перейдите в папку **cmake\\sensorbox_app\\Release**.

1. Выполните следующую команду:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Примените обозреватель Интернета вещей Azure для взаимодействия с устройством IoT Plug and Play, которое подключено к вашему центру Интернета вещей. Дополнительные сведения см. в статье [об установке и настройке обозревателя Интернета вещей Azure](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Дополнительная информация

Итак, вы создали устройство IoT Plug and Play, готовое к сертификации. Теперь мы предлагаем перейти к следующему руководству:

> [!div class="nextstepaction"]
> [Создание устройства, готового к сертификации](tutorial-build-device-certification.md)
