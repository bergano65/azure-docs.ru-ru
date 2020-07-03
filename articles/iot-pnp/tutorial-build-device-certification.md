---
title: Создание готового к сертификации устройства IoT Plug and Play (предварительная версия) | Документация Майкрософт
description: В этой статье разработчик устройств сможет ознакомиться со сведениями о создании готового к сертификации устройства IoT Plug and Play (предварительная версия).
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e97aa07d2a43a03805fd881c674157ee676c37b4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239908"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Создание готового к сертификации устройства IoT Plug and Play (предварительная версия)

В этом учебнике описывается, как разработчик устройств может создать готовое к сертификации устройство IoT Plug and Play (предварительная версия).

В процессе выполнения сертификационных тестов выполняется проверка следующего:

- Код устройства IoT Plug and Play установлен на вашем устройстве.
- Код устройства IoT Plug and Play создан с помощью пакета SDK для Azure IoT.
- Код устройства поддерживает [Службу подготовки устройств к добавлению в Центр Интернета вещей](../iot-dps/about-iot-dps.md).
- Код устройства реализует интерфейс со сведениями об устройстве.
- Модель возможностей и код устройства работают с IoT Central.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником необходимы указанные ниже компоненты.

- [Visual Studio Code](https://code.visualstudio.com/download)
- Пакет расширения [Azure IoT Tools для VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

Кроме того, необходимо завершить быстрое руководство [Использование модели возможностей устройства для создания устройства](quickstart-create-pnp-device-windows.md) для Windows. В кратком руководстве показано, как настроить среду разработки с помощью Vcpkg и создать пример проекта.

## <a name="store-a-capability-model-and-interfaces"></a>Сохранение модели возможностей и интерфейсов

Для устройств IoT Plug and Play необходимо создать модель возможностей и интерфейсы, которые определяют возможности устройства в виде JSON-файлов.

Эти JSON-файлы можно сохранить в трех разных расположениях:

- в общедоступном репозитории моделей;
- в репозитории моделей вашей компании;
- на устройстве.

В настоящее время для сертификации вашего устройства файлы должны храниться либо в репозитории моделей вашей компании, либо в общедоступном репозитории моделей.

## <a name="include-the-required-interfaces"></a>Добавление необходимых интерфейсов

Чтобы пройти процесс сертификации, нужно добавить интерфейс со **сведениями об устройстве** в модель возможностей и реализовать его. Этот интерфейс имеет следующую идентификацию:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Если вы уже выполнили действия, описанные в [кратком руководстве по созданию устройства с помощью модели возможностей устройства](quickstart-create-pnp-device-windows.md), интерфейс со **сведениями об устройстве** уже должен быть добавлен в вашу модель.

Чтобы добавить интерфейс со **сведениями об устройстве** в модель устройства, добавьте идентификатор интерфейса в свойство модели возможностей `implements`:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Чтобы просмотреть интерфейс со **сведениями об устройстве** в VS Code, сделайте следующее:

1. Нажмите сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд.

1. Введите **Plug and Play**, а затем выберите команду **IoT Plug and Play Open Model Repository** (Открыть репозиторий моделей Plug and Play). Выберите **Open Public Model Repository** (Открыть общедоступный репозиторий моделей). Общедоступный репозиторий моделей откроется в VS Code.

1. В открывшемся общедоступном репозитории моделей перейдите на вкладку **Интерфейсы**, выберите значок фильтра и введите **Сведения об устройстве** в поле фильтра.

1. Чтобы создать локальную копию интерфейса со **сведениями об устройстве**, выберите его в отфильтрованном списке, а затем щелкните **Скачать**. В VS Code отобразится файл интерфейса.

Чтобы просмотреть интерфейс **Сведения об устройстве** в Azure CLI, сделайте следующее:

1. [Установите расширение Azure CLI для Интернета вещей](howto-install-pnp-cli.md).

1. Используйте следующую команду Azure CLI, чтобы отобразить интерфейс с идентификатором интерфейса сведений об устройстве:

    ```azurecli
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Дополнительные сведения см. в статье [об установке и настройке расширения Azure CLI для Интернета вещей](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Обновление кода устройства

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Выполнение подготовки устройств с помощью Службы подготовки устройств к добавлению в Центр Интернета вещей

Чтобы сертифицировать устройство, его необходимо подготовить с помощью [Службы подготовки устройств к добавлению в Центр Интернета вещей (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Чтобы добавить возможность использования DPS, вы можете создать заглушку кода C в VS Code. Выполните следующие действия.

1. Откройте папку с файлом DCM в VS Code, нажмите сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд, введите **IoT Plug and Play** и выберите **Generate Device Code Stub** (Создать заглушку кода устройства).

1. Выберите файл DCM, который будет использоваться для создания заглушки кода устройства.

1. Введите название проекта, например **sample_device**. Это будет именем вашего устройства.

1. Выберите язык **ANSI C**.

1. В качестве метода подключения выберите **Via DPS (Device Provisioning Service) symmetric key** (С помощью симметричного ключа DPS (Служба подготовки устройств)).

1. Выберите **CMake Project on Windows** в качестве шаблона проекта.

1. Выберите **Via Vcpkg** (С помощью Vcpkg) в качестве способа добавления пакета SDK для устройств.

1. В VS Code откроется новое окно, содержащее созданные файлы заглушек кода устройства.

## <a name="build-and-run-the-code"></a>Сборка и выполнение кода

Используйте пакет Vcpkg для сборки созданной заглушки кода устройства. Создаваемое приложение имитирует устройство, которое подключается к Центру Интернета вещей. Оно отправляет данные телеметрии и свойства, а также получает команды.

1. Создайте подкаталог `cmake` в папке `sample_device` и перейдите в эту папку.

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Выполните приведенные ниже команды, чтобы выполнить сборку заглушки кода (заменив заполнитель каталогом репозитория Vcpkg).

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > При использовании Visual Studio 2017 или Visual Studio 2015 необходимо указать генератор CMake в соответствии с используемыми средствами сборки.
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Если cmake не может найти ваш компилятор C++, при запуске предыдущей команды отобразятся ошибки сборки. В этом случае попробуйте выполнить эту команду в [командной строке Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. После успешного завершения сборки, введите учетные данные DPS (**область идентификатора DP**, **симметричный ключ DPS**, **идентификатор устройства**) в качестве параметров приложения. Получение учетных данных с портала сертификации описано в статье [Создание готового к сертификации устройства IoT Plug and Play (предварительная версия)](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Реализация стандартных интерфейсов

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Реализация интерфейсов со сведениями о модели и пакете SDK

Пакет SDK для устройств Azure IoT реализует интерфейсы со сведениями о модели и пакете SDK. Если вы используете функцию создания кода в VS Code, код вашего устройства будет использовать пакет SDK для устройств IoT Plug and Play.

Если вы решили не использовать пакет SDK для устройств Azure IoT, можете использовать исходный код пакета SDK в качестве справочного материала для реализации.

#### <a name="implement-the-device-information-interface"></a>Реализация интерфейса со сведениями об устройстве

Реализуйте интерфейс со **сведениями об устройстве** на своем устройстве и предоставьте сведения об устройстве, полученные во время выполнения.

Вы можете использовать пример реализации интерфейса со **сведениями об устройстве** для [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) в качестве справки.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Реализация всех возможностей, определенных в модели

Во время сертификации ваше устройство тестируется программным способом для проверки того, реализует ли оно возможности, определенные в интерфейсах. Используйте код состояния HTTP 501, чтобы отвечать на запросы свойств и команд для чтения и записи, если ваше устройство их не реализует.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы создали устройство IoT Plug and Play, готовое к сертификации, мы предлагаем ознакомиться со следующим руководством:

> [!div class="nextstepaction"]
> [Tutorial: Certify your IoT Plug and Play Preview device](tutorial-certification-test.md) (Руководство по сертификации устройств IoT Plug and Play (предварительная версия))
