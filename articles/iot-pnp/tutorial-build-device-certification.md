---
title: Создание готового к сертификации устройства IoT Plug and Play (предварительная версия) | Документация Майкрософт
description: В этой статье разработчик устройств сможет ознакомиться со сведениями о создании готового к сертификации устройства IoT Plug and Play (предварительная версия).
author: tbhagwat3
ms.author: tanmayb
ms.date: 06/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 019b2ba0fd87610195ca9e6c7cb749be9542bd72
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858857"
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

Кроме того, вам потребуется устройство IoT Plug and Play, созданное при изучении статьи [Quickstart: Use a device capability model to create an IoT Plug and Play device](quickstart-create-pnp-device.md) (Краткое руководство по созданию устройства с помощью модели возможностей устройств).

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
> Если вы уже выполнили действия, описанные в [кратком руководстве по созданию устройства с помощью модели возможностей устройства](quickstart-create-pnp-device.md), интерфейс со **сведениями об устройстве** уже должен быть добавлен в вашу модель.

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
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

Чтобы просмотреть интерфейс со **сведениями об устройстве** в VS Code, сделайте следующее:

1. Нажмите сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд.

1. Введите **Plug and Play**, а затем выберите команду **IoT Plug and Play Open Model Repository** (Открыть репозиторий моделей Plug and Play). Выберите **Open Public Model Repository** (Открыть общедоступный репозиторий моделей). Общедоступный репозиторий моделей откроется в VS Code.

1. В открывшемся общедоступном репозитории моделей перейдите на вкладку **Интерфейсы**, выберите значок фильтра и введите **Сведения об устройстве** в поле фильтра.

1. Чтобы создать локальную копию интерфейса со **сведениями об устройстве**, выберите его в отфильтрованном списке, а затем щелкните **Скачать**. В VS Code отобразится файл интерфейса.

## <a name="update-device-code"></a>Обновление кода устройства

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Выполнение подготовки устройств с помощью Службы подготовки устройств к добавлению в Центр Интернета вещей

Чтобы сертифицировать устройство, его необходимо подготовить с помощью [Службы подготовки устройств к добавлению в Центр Интернета вещей (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Чтобы добавить возможность использования DPS, вы можете создать заглушку кода C в VS Code. Выполните следующие действия.

1. Откройте папку с файлом DCM в VS Code, нажмите сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд, введите **IoT Plug and Play** и выберите **Generate Device Code Stub** (Создать заглушку кода устройства).

1. Выберите файл DCM, который будет использоваться для создания заглушки кода устройства.

1. Введите название проекта — это имя вашего устройства приложения.

1. Выберите язык **ANSI C**.

1. Выберите **Проект CMake** в качестве типа проекта.

1. В качестве метода подключения выберите **Via DPS (Device Provisioning Service) symmetric key** (С помощью симметричного ключа DPS (Служба подготовки устройств)).

1. В VS Code откроется новое окно, содержащее созданные файлы заглушек кода устройства.

1. Откройте файл `main.c`, укажите подготовленные значения **dpsIdScope**, **sasKey** и **registrationId**. Эти сведения можно найти на портале сертификации. Дополнительные сведения см. в разделе [Подключение и обнаружение интерфейсов](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```c
    // TODO: Specify DPS scope ID if you intend on using DPS / IoT Central.
    static const char *dpsIdScope = "[DPS Id Scope]";
    
    // TODO: Specify symmetric keys if you intend on using DPS / IoT Central and symmetric key based auth.
    static const char *sasKey = "[DPS symmetric key]";
    
    // TODO: specify your device registration ID
    static const char *registrationId = "[device registration Id]";
    ```

1. Сохраните файл.

### <a name="implement-standard-interfaces"></a>Реализация стандартных интерфейсов

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Реализация интерфейсов со сведениями о модели и пакете SDK

Пакет SDK для устройств Azure IoT реализует интерфейсы со сведениями о модели и пакете SDK. Если вы используете функцию создания кода в VS Code, код вашего устройства будет использовать пакет SDK для устройств IoT Plug and Play.

Если вы решили не использовать пакет SDK для устройств Azure IoT, можете использовать исходный код пакета SDK в качестве справочного материала для реализации.

#### <a name="implement-the-device-information-interface"></a>Реализация интерфейса со сведениями об устройстве

Реализуйте интерфейс со **сведениями об устройстве** на своем устройстве и предоставьте сведения об устройстве, полученные во время выполнения.

Вы можете использовать пример реализации интерфейса со **сведениями об устройстве** для [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) в качестве справки.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Реализация всех возможностей, определенных в модели

Во время сертификации ваше устройство тестируется программным способом для проверки того, реализует ли оно возможности, определенные в интерфейсах. Используйте код состояния HTTP 501, чтобы отвечать на запросы свойств и команд для чтения и записи, если ваше устройство их не реализует.

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы создали устройство IoT Plug and Play, готовое к сертификации, мы предлагаем ознакомиться со следующим руководством:

> [!div class="nextstepaction"]
> [Tutorial: Certify your IoT Plug and Play Preview device](tutorial-certification-test.md) (Руководство по сертификации устройств IoT Plug and Play (предварительная версия))
