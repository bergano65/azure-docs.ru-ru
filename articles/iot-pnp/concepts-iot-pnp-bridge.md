---
title: Мост Plug and Play IoT | Документация Майкрософт
description: Сведения о Bridge Plug and Play центра Интернета вещей и о том, как использовать его для подключения существующих устройств, подключенных к шлюзу Windows или Linux, в качестве устройств IoT Plug and Play.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 224f86a40fa812003463301f97bcae07de907f3c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580720"
---
# <a name="iot-plug-and-play-bridge"></a>Мост Plug and Play IoT

Мост IoT Plug and Play — это приложение с открытым исходным кодом для подключения существующих устройств, подключенных к шлюзу Windows или Linux, в качестве устройств IoT Plug and Play. После установки и настройки приложения на компьютере под управлением Windows или Linux его можно использовать для подключения подключенных устройств к центру Интернета вещей. С помощью моста можно сопоставлять интерфейсы Интернета вещей Plug and Play с данными телеметрии, отправляемыми подключенными устройствами, работать со свойствами устройств и вызывать команды.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="В левой части существует несколько подключенных датчиков (как проводные, так и беспроводные) на компьютер под управлением Windows или Linux, содержащий мост Интернета вещей Plug and Play. Затем мост IoT Plug and Play соединяется с центром Интернета вещей справа":::

Мост Интернета вещей Plug and Play можно развернуть в виде автономного исполняемого файла на любом устройстве Интернета вещей, промышленном ПК, сервере или шлюзе под управлением Windows 10 или Linux. Его также можно скомпилировать в код приложения. Простой JSON-файл конфигурации сообщает центру Интернета вещей Plug and Play Bridge, какие подключенные устройства и периферийное устройство должны быть доступны в Azure.

## <a name="supported-protocols-and-sensors"></a>Поддерживаемые протоколы и датчики

Мост IoT Plug and Play поддерживает следующие типы периферийных устройств по умолчанию со ссылками на документацию по адаптеру:

|Периферийные устройства|Windows|Linux|
|---------|---------|---------|
|[Bluetooth с низким энергопотреблением](https://aka.ms/iot-pnp-bridge-bluetooth)       |Да|Нет|
|[Камеры.](https://aka.ms/iot-pnp-bridge-camera)               |Да|Нет|
|[Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |Да|Да|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |Да|Да|
|[Подключение](https://aka.ms/iot-pnp-bridge-serial)                |Да|Да|
|[Периферийные устройства USB Windows](https://aka.ms/iot-pnp-bridge-usb)  |Да|Не применимо|

>[!Important]
>Разработчики могут расширить мост Интернета вещей Plug and Play для поддержки дополнительных протоколов устройств, следуя инструкциям в документации по средству для **[разработчиков центра Интернета вещей Plug and Play](https://aka.ms/iot-pnp-bridge-dev-doc)**.

## <a name="prerequisites"></a>Предварительные требования

### <a name="os-platform"></a>Платформа ОС

Поддерживаются следующие платформы и версии ОС:

|Платформа  |Поддерживаемые версии  |
|---------|---------|
|Windows 10 |     Поддерживаются все номера SKU Windows. Например: IoT Корпоративная, сервер, Настольный компьютер, IoT Core. *Для функций мониторинга работоспособности камеры рекомендуется использовать 20H1 или более позднюю сборку. Все остальные функции доступны во всех сборках Windows 10.*  |
|Linux     |Протестированные и поддерживаемые в Ubuntu 18,04, функции других дистрибутивов не были протестированы.         |
||

### <a name="hardware"></a>Оборудование

- Любая аппаратная платформа, поддерживающая указанные выше номера SKU и версии ОС.
- Устройства с последовательными портами, USB, Bluetooth, камеры и датчики поддерживаются изначально. Мост центра Интернета вещей Plug and Play можно расширить для поддержки любого настраиваемого периферийного устройства или датчика ([см. раздел выше](#iot-plug-and-play-bridge)).

### <a name="development-environment"></a>Среда разработки

Для сборки, расширения и разработки центра Интернета вещей Plug and Play вам потребуется:  

- Среда разработки, поддерживающая компиляцию C++, например [Visual Studio (Community, Professional или Enterprise)](https://visualstudio.microsoft.com/downloads/). при установке Visual Studio убедитесь, что вы включили рабочую нагрузку Разработка классических приложений на C++.
- [CMAK](https://cmake.org/download/) — при установке CMAK выберите параметр `Add CMake to the system PATH` .
- При построении на Windows необходимо также загрузить пакет SDK для Windows 17763: [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
- [Пакет SDK для устройства с центром Интернета вещей Azure](https://github.com/Azure/azure-iot-sdk-c). Прилагаемые скрипты сборки в этом репозитории будут автоматически клонировать необходимый пакет SDK для Azure IoT C.

### <a name="azure-iot-products-and-tools"></a>Продукты и инструменты Azure IoT

- **Центр Интернета вещей Azure** — для подключения устройства к требуется [центр Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub/) в подписке Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу. Если у вас нет центра Интернета вещей, [следуйте этим инструкциям, чтобы создать его](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli).

> [!Note]
> Сейчас функция IoT Plug and Play доступна в центрах Интернета вещей, созданных в таких регионах, как Центральная часть США, Северная Европа и Восточная Япония. Поддержка Plug and Play IoT не включена в центры Интернета вещей ценовой категории "Базовый". Для взаимодействия с устройством Plug and Play IoT можно использовать средство Azure IoT Explorer. [Скачайте и установите последний выпуск обозревателя Интернета вещей Azure](./howto-use-iot-explorer.md) для вашей операционной системы.

## <a name="iot-plug-and-play-bridge-architecture"></a>Архитектура моста Plug and Play IoT

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="В левой части существует несколько подключенных датчиков (как проводные, так и беспроводные) на компьютер под управлением Windows или Linux, содержащий мост Интернета вещей Plug and Play. Затем мост IoT Plug and Play соединяется с центром Интернета вещей справа":::

## <a name="download-iot-plug-and-play-bridge"></a>Скачивание моста Plug and Play IoT

Вы можете скачать предварительно созданную версию моста с поддерживаемыми адаптерами в [IoT Plug and Play мостовыми выпусками](https://aka.ms/iot-pnp-bridge-releases) и расширить список ресурсов для самого последнего выпуска. Скачайте последнюю версию приложения для вашей операционной системы.

Кроме того, вы можете загрузить и просмотреть исходный код [центра Интернета вещей Plug and Play Bridge на GitHub](https://aka.ms/bridge).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда у вас есть общие сведения об архитектуре центра Интернета вещей Plug and Play, выполните следующие действия, чтобы получить дополнительные сведения:

- [Как использовать Plug and Play моста Интернета вещей](./howto-use-iot-pnp-bridge.md)
- [См. Справочник разработчика GitHub для центра Интернета вещей Plug and Play Bridge](https://aka.ms/iot-pnp-bridge-dev-doc)
- [Мост IoT Plug and Play на GitHub](https://aka.ms/iotplugandplaybridge)
