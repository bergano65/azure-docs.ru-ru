---
title: Подключение устройства Raspberry Pi к приложению Azure IoT Central (Python) | Документация Майкрософт
description: Как разработчик устройства, как подключить Raspberry Pi к приложению Azure IoT Central с помощью Python.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: bd506bf1210692feb017f3b526c3b6d4bca36004
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877430"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Подключение устройства Raspberry Pi к приложению Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

В этой статье описано, каким образом разработчик устройства может подключить устройство Raspberry Pi к приложению Microsoft Azure IoT Central, используя язык Python.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, вам потребуются следующие компоненты:

* Приложение Azure IoT Central, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits). Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).
* Устройство Raspberry Pi с операционной системой Raspbian. Raspberry Pi должен иметь возможность подключения к Интернету. Дополнительные сведения см. в разделе [Настройка устройства Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>Пример **приложения Devkits**

Приложение, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits), включает в себя шаблон приложения **Raspberry Pi** со следующими характеристиками:

- Данные телеметрии. К ним относятся следующие показатели, которые устройство будет собирать:
  - Влажность
  - Температура
  - Давление
  - магнитометр (X, Y, Z);
  - акселерометр (X, Y, Z);
  - гироскоп (X, Y, Z).
- Настройки
  - Напряжение
  - Текущий
  - Скорость вращения вентилятора
  - ИК-переключатель.
- Свойства
  - Свойство серийного номера устройства
  - Свойство расположения облака

Полные сведения о конфигурации шаблона устройства см. в разделе [сведения о шаблоне устройства Raspberry Pi](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Добавление реального устройства

В приложении IoT Central Azure добавьте реальное устройство из шаблона устройства **Raspberry Pi** . Запишите сведения о подключении устройства (**идентификатор области**, **идентификатор устройства**и **первичный ключ**). Дополнительные сведения см. в статье [Добавление реального устройства в приложение Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Настройка Raspberry Pi

Далее описывается, как скачать и настроить образец приложения Python из GitHub. Этот пример приложения:

* отправляет данные телеметрии и значения свойств в Azure IoT Central;
* реагирует на изменения параметров, внесенные в Azure IoT Central.

Чтобы настроить устройство, выполните пошаговые [инструкции на сайте GitHub](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md).

1. Когда устройство настроено, устройство начинает отправлять измерения телеметрии в Azure IoT Central.
1. В приложении Azure IoT Central вы увидите, как код, выполняющийся в Raspberry Pi, взаимодействует с приложением:

    * На странице **Measurements** (Измерения) для реального устройства можно просмотреть данные телеметрии, отправленные из Raspberry Pi.
    * На странице **Параметры** можно изменить параметры Raspberry Pi, такие как напряжение и скорость вращения вентилятора. Когда Raspberry Pi подтверждает изменение, параметр отображается как **синхронизированный**.

## <a name="raspberry-pi-device-template-details"></a>Сведения о шаблоне устройства Raspberry Pi

Приложение, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits), включает в себя шаблон приложения **Raspberry Pi** со следующими характеристиками:

### <a name="telemetry-measurements"></a>Измерения телеметрии

| Имя поля     | Единицы  | Минимум | Максимум | Число десятичных знаков |
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

### <a name="settings"></a>Настройки

Числовые параметры

| `Display name` | Имя поля | Единицы | Число десятичных знаков | Минимум | Максимум | Исходный |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Напряжение      | setVoltage | В | 0              | 0       | 240     | 0       |
| Текущий      | setCurrent | Амперы  | 0              | 0       | 100     | 0       |
| Скорость вращения вентилятора    | fanSpeed   | Об/мин   | 0              | 0       | 1000    | 0       |

Параметры переключения

| `Display name` | Имя поля | Включение текста | Отключение текста | Исходный |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ВКЛ.      | ВЫКЛ.      | Выкл     |

### <a name="properties"></a>Свойства

| Тип            | `Display name` | Имя поля | Тип данных |
| --------------- | ------------ | ---------- | --------- |
| Свойство устройства | Серийный номер   | dieNumber  | номер    |
| Текст            | Местоположение     | расположение   | Н/Д       |

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали, как подключить Raspberry Pi к приложению Azure IoT Central, предлагаем следующий шаг — Узнайте, как [настроить пользовательский шаблон устройства](howto-set-up-template.md) для своего устройства IOT.
