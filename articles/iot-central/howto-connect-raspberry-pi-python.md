---
title: Подключение устройства Raspberry Pi к приложению Azure IoT Central (Python) | Документация Майкрософт
description: Как разработчик устройств подключение устройства Raspberry Pi к Azure IoT Central приложения с помощью Python.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: eccc4100c89c971e264b9b915cd17b9f5ce4477b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64405876"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Подключение устройства Raspberry Pi к приложению Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

В этой статье описано, каким образом разработчик устройства может подключить устройство Raspberry Pi к приложению Microsoft Azure IoT Central, используя язык Python.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, вам потребуются следующие компоненты:

* Приложение Azure IoT Central, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits). Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).
* Устройство Raspberry Pi с операционной системой Raspbian. Raspberry Pi необходимо иметь возможность подключения к Интернету. Дополнительные сведения см. в разделе [Настройка Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>Пример **приложения Devkits**

Приложение, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits), включает в себя шаблон приложения **Raspberry Pi** со следующими характеристиками:

- Данные телеметрии. К ним относятся следующие показатели, которые устройство будет собирать:
  - влажность.
  - температура;
  - Давление
  - магнитометр (X, Y, Z);
  - акселерометр (X, Y, Z);
  - гироскоп (X, Y, Z).
- Параметры
  - Напряжение
  - Текущее значение
  - Скорость вращения вентилятора
  - ИК-переключатель.
- properties
  - Свойство серийного номера устройства
  - Свойство расположения облака

Подробные сведения о конфигурации шаблона устройства, см. в разделе [сведения о шаблоне устройство Raspberry Pi](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Добавление реального устройства

В приложении Azure IoT Central, добавьте реального устройства из **Raspberry Pi** шаблон устройства. Запомните или запишите устройства сведения о подключении (**идентификатор области**, **идентификатор устройства**, и **первичный ключ**). Дополнительные сведения см. в статье [Добавление реального устройства в приложение Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Настройка Raspberry Pi

Далее описывается, как скачать и настроить образец приложения Python из GitHub. Этот пример приложения:

* отправляет данные телеметрии и значения свойств в Azure IoT Central;
* реагирует на изменения параметров, внесенные в Azure IoT Central.

Для настройки устройства, [Следуйте пошаговым инструкциям на сайте GitHub](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md).

1. Настроив устройство, устройство начнет отправку телеметрии измерения для Azure IoT Central.
1. В приложении Azure IoT Central вы увидите, как код, выполняющийся в Raspberry Pi, взаимодействует с приложением:

    * На странице **Measurements** (Измерения) для реального устройства можно просмотреть данные телеметрии, отправленные из Raspberry Pi.
    * На **параметры** страницы, можно изменить параметры на устройстве Raspberry Pi, таких как скорость напряжения и вентилятора. Когда устройство Raspberry Pi подтверждает изменения, в параметрах показано, как **синхронизированные**.

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi шаблона подробной информации

Приложение, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits), включает в себя шаблон приложения **Raspberry Pi** со следующими характеристиками:

### <a name="telemetry-measurements"></a>Измерения телеметрии

| Имя поля     | Units  | Минимальная | Максимальная | Число десятичных знаков |
| -------------- | ------ | ------- | ------- | -------------- |
| Влажность       | %      | 0       | 100     | 0              |
| temp           | °C     | –40     | 120     | 0              |
| pressure       | гПа    | 260     | 1260    | 0              |
| magnetometerX  | мГс | –1000   | 1000    | 0              |
| magnetometerY  | мГс | –1000   | 1000    | 0              |
| magnetometerZ  | мГс | –1000   | 1000    | 0              |
| accelerometerX | mg     | –2000   | 2000    | 0              |
| accelerometerY | mg     | –2000   | 2000    | 0              |
| accelerometerZ | mg     | –2000   | 2000    | 0              |
| gyroscopeX     | милиградусов/с   | –2000   | 2000    | 0              |
| gyroscopeY     | милиградусов/с   | –2000   | 2000    | 0              |
| gyroscopeZ     | милиградусов/с   | –2000   | 2000    | 0              |

### <a name="settings"></a>Параметры

Числовые параметры

| `Display name` | Имя поля | Units | Число десятичных знаков | Минимальная | Максимальная | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Напряжение      | setVoltage | В | 0              | 0       | 240     | 0       |
| Текущее значение      | setCurrent | Амперы  | 0              | 0       | 100     | 0       |
| Скорость вращения вентилятора    | fanSpeed   | Об/мин   | 0              | 0       | 1000    | 0       |

Параметры переключения

| `Display name` | Имя поля | Включение текста | Отключение текста | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ВКЛ      | ВЫКЛ.      | Отключить     |

### <a name="properties"></a>properties

| type            | `Display name` | Имя поля | Тип данных |
| --------------- | ------------ | ---------- | --------- |
| Свойство устройства | Серийный номер   | dieNumber  | номер    |
| Text            | Расположение     | location   | Н/Д       |

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как для подключения устройства Raspberry Pi к Azure IoT Central приложения, мы предлагаем — Узнайте, как [настроить шаблон пользовательского устройства](howto-set-up-template.md) для устройства Интернета вещей.
