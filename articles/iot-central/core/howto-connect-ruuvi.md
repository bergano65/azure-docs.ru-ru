---
title: Подключите RuuviTag в Azure IoT Central Документы Майкрософт
description: Узнайте, как подключить датчик среды RuuviTag к приложению IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 93e4d3d0bed9090573d2b6ee87a29b86ccd72e42
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758938"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Подключите датчик RuuviTag к приложению Azure IoT Central

*Эта статья относится к разработчикам решений и разработчикам устройств.*

В этой статье описывается, как, будучи разработчиком решений, можно подключить датчик RuuviTag к приложению Microsoft Azure IoT Central.

Что такое тег Ruuvi?

RuuviTag — это передовая платформа с открытым исходным кодом, предназначенная для удовлетворения потребностей бизнес-клиентов, разработчиков, производителей, студентов и любителей. Устройство настроено на работу, как только вы берете его из коробки и готов к развертыванию, где вам это нужно. Это Bluetooth LE маяк с датчиком окружающей среды и акселерометр встроенный.

RuuviTag общается по всему BLE (Bluetooth Low Energy) и требует устройства шлюза для разговора с Azure IoT Central. Убедитесь, что у вас есть устройство шлюза, например, Rigado Cascade 500, для подключения RuuviTag к IoT Central.

Пожалуйста, следуйте [инструкциям здесь,](./howto-connect-rigado-cascade-500.md) если вы хотите настроить Устройство Rigado Cascade 500 шлюз.

## <a name="prerequisites"></a>Предварительные требования

Для подключения датчиков RuuviTag необходимы следующие ресурсы:

* Датчик RuuviTag. Для получения дополнительной информации, пожалуйста, посетите [RuuviTag](https://ruuvi.com/).
* Устройство Rigado Cascade 500 или другой шлюз BLE. Для получения дополнительной информации, пожалуйста, посетите [Ригадо](https://www.rigado.com/).
* Приложение Azure IoT Central. Для получения дополнительной информации смотрите [создание нового приложения](./quick-deploy-iot-central.md).

## <a name="add-a-ruuvitag-device-template"></a>Добавить шаблон устройства RuuviTag

Чтобы сесть на датчик RuuviTag в экземпляр приложения Azure IoT Central, необходимо настроить соответствующий шаблон устройства в приложении.

Чтобы добавить шаблон устройства RuuviTag:

1. Перейдите на вкладку ***шаблонов устройств*** в левом ![стека,](./media/howto-connect-ruuvi/devicetemplate-new.png) выберите **новый**: Создайте новый шаблон устройства Страница дает вам возможность ***создать пользовательский шаблон*** или ***использовать пренастроенный шаблон устройства***
1. Выберите шаблон устройства RuuviTag из списка предварительно настроенных шаблонов устройств, как показано ниже: ![Выберите шаблон устройства RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Выберите ***следующий: Настроить,*** чтобы продолжить следующий шаг.
1. На следующем экране выберите ***Создать*** на борту шаблона устройства C500 в вашем приложении IoT Central.

## <a name="connect-a-ruuvitag-sensor"></a>Подключение датчика RuuviTag

Как упоминалось ранее, чтобы подключить RuuviTag к приложению IoT Central, необходимо настроить устройство шлюза. Приведенные ниже шаги предполагают, что вы создали устройство шлюза Rigado Cascade 500.  

1. Питание на устройстве Rigado Cascade 500 и подключение к подключению к сети (через Ethernet или беспроводной связь)
1. Поп крышку от RuuviTag и вытащить пластиковые вкладке для обеспечения связи с батареей.
1. Поместите RuuviTag рядом с шлюзом Rigado Cascade 500, который уже настроен в вашем приложении IoT Central.
1. Всего за несколько секунд ваш RuuviTag должен появиться в вашем списке устройств в IoT Central.  
    ![Список устройств RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Теперь вы можете использовать этот RuuviTag в приложении IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Создание смоделированного RuuviTag

Если у вас нет физического устройства RuuviTag, можно создать смоделированный датчик RuuviTag для тестирования в приложении Azure IoT Central.

Для создания смоделированного RuuviTag:

1. Выберите **устройства > RuuviTag**.
1. Выберите **+ Создать**.
1. Укажите уникальный **идентификатор устройства** и имя дружественного **устройства.**  
1. Включить **смоделированную настройку.**
1. Нажмите кнопку **создания**.  

## <a name="next-steps"></a>Next Steps

Если вы разработчик устройств, некоторые предлагаемые следующие шаги:

- Читайте о [подключении устройств в Azure IoT Central](./concepts-get-connected.md)
- Узнайте, как [отслеживать подключение к устройствам с помощью Azure CLI](./howto-monitor-devices-azure-cli.md)
