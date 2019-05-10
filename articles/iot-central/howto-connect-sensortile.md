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
ms.openlocfilehash: 8c1b4a4ab834b2203a7e0b6e4e9e366c3fc38774
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472171"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Подключение устройства SensorTile.box к приложению Azure IoT Central

В этой статье описывается как разработчик устройств для подключения устройства SensorTile.box приложение Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, вам понадобятся следующие ресурсы:

* Устройство SensorTile.box. Дополнительные сведения см. в разделе [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* Датчик BLE ST установленное приложение на устройстве Android, вы можете [скачать отсюда](https://play.google.com/store/apps/details?id=com.st.bluems). Для получения дополнительных сведений посетите: [Датчик BLE ST](https://www.st.com/stblesensor)
* Приложение Azure IoT Central, созданное с **DevKits** шаблон приложения. Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).
* Добавить **SensorTile.box** шаблон устройства в приложении IoT Central, посетив **шаблонов устройств** страницу, нажав кнопку **+ создать**и выбрав **SensorTile.box** шаблона.

### <a name="get-your-device-connection-details"></a>Получение сведений о подключении устройства

В приложении Azure IoT Central, добавьте реального устройства из **SensorTile.box** шаблон устройства и запишите сведения о подключении устройства: **Код области**, **идентификатор устройства**, и **первичный ключ**:

1. Добавление устройства в Device Explorer. Выберите **+ Создать > реальных** Добавление реального устройства.

    * Введите в нижнем регистре **идентификатор устройства**, или используйте предложенные **идентификатор устройства**.
    * Введите **имя_устройства**, или используйте предложенное имя

    ![Добавить устройство](media/howto-connect-sensortile/real-device.png)

1. Чтобы получить сведения о подключении устройства **идентификатор области**, **идентификатор устройства**, и **первичный ключ**выберите **Connect** на странице "устройства".

    ![Сведения о подключении](media/howto-connect-sensortile/connect-device.png)

1. Запомните или запишите сведения о подключении. При подготовке устройство DevKit на следующем шаге вы временно отключен от Интернета.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Настройка SensorTile.box с мобильным приложением

В этом разделе вы узнаете, как для принудительной отправки приложения встроенного по на устройство. Затем вы как отправлять данные устройства в центр Интернета вещей через мобильное приложение ST BLE датчика с помощью подключения Bluetooth низким энергопотреблением (BLE).

1. Откройте приложение BLE датчик ST и нажмите клавишу **создать новое приложение** кнопки.

    ![Создать приложение](media/howto-connect-sensortile/create-app.png)

1. Выберите **показатель связанности** приложения.
1. Нажмите кнопку upload.

    ![Показатель загрузки](media/howto-connect-sensortile/barometer-upload.png)

1. Нажмите кнопку воспроизведения, связанный с вашей SensorTile.box.
1. По завершении процесса SensorTile.box выполняет потоковую передачу температуры, давления и влажности по BLE.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Подключение к облаку SensorTile.box

В этом разделе вы научитесь подключаться SensorTile.box мобильными приложениями и мобильных приложений в облако.

1. С помощью меню слева выберите **ведение журнала Cloud** кнопки.

    ![Ведение журнала для облака](media/howto-connect-sensortile/cloud-logging.png)

1. Выберите **Azure IoT Central** как поставщик облака.
1. Вставьте идентификатор устройства и идентификатор области, которые упоминались ранее.

    ![Учетные данные](media/howto-connect-sensortile/credentials.png)

1. Выберите **ключ приложения** "переключатель".
1. Нажмите кнопку **Connect** и выбрать данные телеметрии, который необходимо передать.
1. Через несколько секунд данные отображаются на панели мониторинга приложения IoT Central.

## <a name="sensortilebox-device-template-details"></a>Сведения о шаблоне SensorTile.box устройства

Приложения, созданного на основе шаблона SensorTile.box устройства со следующими характеристиками:

### <a name="telemetry"></a>Телеметрия

| Имя поля     | Единицы  | Минимум | Максимум | Число десятичных знаков |
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

Теперь, когда вы узнали, как соединиться с SensorTile.box приложение Azure IoT Central, мы предлагаем — дополнительные [настройке шаблона пользовательское устройство](howto-set-up-template.md) для устройства Интернета вещей.
