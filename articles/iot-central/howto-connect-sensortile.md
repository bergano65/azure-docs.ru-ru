---
title: Подключение устройства SensorTile.box приложение Azure IoT Central | Документация Майкрософт
description: Как разработчик устройств Узнайте, как подключить устройство SensorTile.box приложение Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 580a8baa19e8ed4fc3f4449ead9d8aedbc4c039a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160910"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Подключение устройства SensorTile.box к приложению Azure IoT Central

В этой статье описывается как разработчик устройств для подключения устройства SensorTile.box приложение Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, вам понадобятся следующие ресурсы:

* Устройство SensorTile.box, см. в разделе [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/SensorTile.box) Дополнительные сведения.
* Датчик BLE ST установленное приложение на устройстве Android, вы можете [скачать его здесь] (https://play.google.com/store/apps/details?id=com.st.bluems). Дополнительные сведения см. в статье: () [датчик BLE ST]http://www.st.com/stblesensor)
* Приложение Azure IoT Central, созданное с **DevKits** шаблон приложения. Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).
* Добавить **SensorTile.box** шаблон устройства в приложении IoT Central, посетив **шаблонов устройств** страницу, нажав кнопку **+ создать**и выбрав **SensorTile** шаблона.

### <a name="get-your-device-connection-details"></a>Получение сведений о подключении устройства

В приложении Azure IoT Central, добавьте реального устройства из **SensorTile.box** шаблон устройства и запишите сведения о подключении устройства: **Область идентификатор, идентификатор устройства и первичный ключ**:

1. Добавление устройства в Device Explorer. Выберите **+ Создать > реальных** Добавление реального устройства.

    * Введите в нижнем регистре **идентификатор устройства**, или используйте предложенные **идентификатор устройства**.
    * Введите **имя_устройства**, или используйте предложенное имя

    ![Add Device (Добавление устройства)](media/howto-connect-sensortile/real-device.png)

1. Чтобы получить сведения о подключении устройства **идентификатор области**, **идентификатор устройства**, и **первичный ключ**выберите **Connect** на странице "устройства".

    ![Сведения о подключении](media/howto-connect-sensortile/connect-device.png)

1. Запомните или запишите сведения о подключении. При подготовке устройство DevKit на следующем шаге вы временно отключен от Интернета.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Настройка SensorTile.box с мобильным приложением

В этом разделе вы узнаете, как можно отправлять встроенного приложения на устройство и отправки данных устройства IoT Central через мобильное приложение ST BLE датчика с помощью подключения Bluetooth низким энергопотреблением (BLE).
1. Откройте приложение BLE датчик ST и нажмите клавишу **создать новое приложение** кнопки.

    ![Создание приложения](media/howto-connect-sensortile/create-app.png)

1. Выберите **показатель связанности** приложения.
1. Нажмите кнопку upload.

    ![Показатель загрузки](media/howto-connect-sensortile/barometer-upload.png)

1. Нажмите кнопку воспроизведения, связанный с вашей SensorTile.box.
1. По завершении процесса SensorTile.box будет потоковая передача температуры, давления и влажности через BLE.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Подключение к облаку SensorTile.box

В этом разделе вы узнаете, как подключаться SensorTile.box мобильными приложениями и мобильных приложений в облако.
1. С помощью меню слева выберите **ведение журнала Cloud** кнопки.

    ![Ведение журнала для облака](media/howto-connect-sensortile/cloud-logging.png)

1. Выберите **Azure IoT Central** как поставщик облака.
1. Вставьте идентификатор устройства и идентификатор области, которые упоминались ранее.

    ![Учетные данные](media/howto-connect-sensortile/credentials.png)

1. Выберите **ключ приложения** "переключатель".
1. Нажмите кнопку **Connect** и выбрать данные телеметрии, который необходимо передать.
1. Через несколько секунд данные будут отображаться на панели мониторинга приложения IoT Central.

## <a name="sensortilebox-device-template-details"></a>Сведения о шаблоне SensorTile.box устройства

Приложения, созданного на основе шаблона SensorTile.box устройства со следующими характеристиками:

### <a name="telemetry"></a>Телеметрия

| Имя поля     | Units  | Минимальная | Максимальная | Число десятичных знаков |
| -------------- | ------ | ------- | ------- | -------------- |
| Влажность       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | мГс | –1000   | 1000    | 0              |
| magnetometerY  | мГс | –1000   | 1000    | 0              |
| magnetometerZ  | мГс | –1000   | 1000    | 0              |
| accelerometerX | mg     | –2000   | 2000    | 0              |
| accelerometerY | mg     | –2000   | 2000    | 0              |
| accelerometerZ | mg     | –2000   | 2000    | 0              |
| gyroscopeX     | DPS   | -3276   | 3276    | 1              |
| gyroscopeY     | DPS   | -3276   | 3276    | 1              |
| gyroscopeZ     | DPS   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |


## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как соединиться с SensorTile.box приложение Azure IoT Central, мы предлагаем — Узнайте, как [настроить шаблон пользовательского устройства](howto-set-up-template.md) для устройства Интернета вещей.
