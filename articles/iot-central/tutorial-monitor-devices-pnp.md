---
title: Мониторинг устройств в Azure IoT Central | Документация Майкрософт
description: Вы, как оператор, можете использовать приложение Azure IoT Central для мониторинга устройств.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878785"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Руководство по использованию Azure IoT Central для мониторинга устройств (предварительные версии функций)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

В этом руководстве показано, как оператору использовать приложение Microsoft Azure IoT Central для мониторинга устройств и изменения параметров.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Получение уведомления
> * изучать проблему;
> * Устранение проблемы

## <a name="prerequisites"></a>Предварительные требования

Прежде чем вы начнете, конструктор должен выполнить инструкции в трех руководствах, чтобы создать приложение Azure IoT Central:

* [Определение нового типа устройства](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Учебник по добавлению устройства](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Настройка правил и действий для устройства](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Получение уведомления

Azure IoT Central отправляет уведомления об устройствах как сообщения электронной почты. Конструктор добавил правило для отправки уведомления в случае, когда температура в подключенном датчике среды превышает пороговое значение. Проверьте сообщения, отправленные в учетную запись, выбранную конструктором для получения уведомлений.

Откройте сообщение, полученное в конце работы с руководством [Настройка правил и действий для своего устройства в Azure IoT Central](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). В письме выберите ссылку на устройство:

![Письма с уведомлениями](media/tutorial-monitor-devices-pnp/email.png)

В браузере откроется представление **Панель мониторинга** для имитированного устройства "Датчик окружающей среды", созданного при работе с предыдущим учебником:

![Устройство, которое активировало сообщение с уведомлением](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>Изучение проблемы

Оператор может просматривать сведения об устройстве на страницах **Панель мониторинга**, **Environmental Sensor properties** (Свойства датчика среды) и **Команды**. Сборщик настроил страницы **Панель мониторинга** и **Environmental Sensor properties** (Свойства датчика среды) для вывода важной информации о подключенном датчике среды.

Выберите представление **Панель мониторинга**, чтобы просмотреть информацию об устройстве.

На панели мониторинга показан график температуры устройства. Вы также можете увидеть текущую целевую температуру устройства на фрагменте **Свойства устройства**. Вы решили, что температура слишком велика.

## <a name="remediate-an-issue"></a>Устранение проблемы

Чтобы внести изменения в устройство, используйте страницу **Environmental Sensor properties** (Свойства датчика среды):

1. Выберите **Environmental Sensor properties** (Свойства датчика среды). Укажите для **уровня яркости** значение 10. Нажмите кнопку **Сохранить**, чтобы обновить устройство. Когда устройство подтверждает изменение параметров, состояние свойства изменяется на **синхронизировано**:

    ![Обновление параметров](media/tutorial-monitor-devices-pnp/change-settings.png)

2. Выберите **Панель мониторинга** и проверьте новое значение свойства:

    ![Обновленная панель мониторинга устройства](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

* Получение уведомления
* изучать проблему;
* Устранение проблемы

Теперь, когда вам известно о мониторинге устройства, рекомендуется выполнить такой следующий шаг:

> [!div class="nextstepaction"]
> [Руководство по настройке правил и действий для устройства в Azure IoT Central](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).