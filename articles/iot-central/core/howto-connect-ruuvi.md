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
ms.openlocfilehash: e8d1c4a605e8db2e9753bb80c9712dd6c2be7b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158262"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Подключите датчик RuuviTag к приложению Azure IoT Central

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
1. Выберите **Создать**.  

## <a name="next-steps"></a>Next Steps

Теперь, когда вы узнали, как подключить RuuviTag к приложению Azure IoT Central, следующий шаг должен научиться [настраивать приложение IoT Central](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) для создания решения от конца до конца.
