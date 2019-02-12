---
title: Мониторинг устройств в Azure IoT Central | Документация Майкрософт
description: Вы, как оператор, можете использовать приложение Azure IoT Central для мониторинга устройств.
author: dominicbetts
ms.author: dobett
ms.date: 02/01/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 9480565643887b5a9a4d644ba3173b365eaea29c
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767779"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Руководство. Использование Azure IoT Central для мониторинга устройств

В этом руководстве показано, как оператору использовать приложение Microsoft Azure IoT Central для мониторинга устройств и изменения параметров.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Получение уведомления
> * изучать проблему;
> * Устранение проблемы

## <a name="prerequisites"></a>Предварительные требования

Прежде чем вы начнете, конструктор должен выполнить инструкции в трех руководствах, чтобы создать приложение Azure IoT Central:

* [Определение нового типа устройства](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Настройка правил и действий для устройства](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Настройка представлений оператора](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Получение уведомления

Azure IoT Central отправляет уведомления об устройствах как сообщения электронной почты. Конструктор добавил правило для отправки уведомления в случае, если температура в подключенном кондиционере превышает порог. Проверьте сообщения, отправленные в учетную запись, выбранную конструктором для получения уведомлений.

Откройте сообщение, полученное в конце работы с руководством [Настройка правил и действий для своего устройства в Azure IoT Central](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). В сообщении выберите **Click here to open your device** (Щелкните здесь, чтобы открыть устройство):

![Письма с уведомлениями](media/tutorial-monitor-devices-experimental/email.png)

В браузере откроется страница **Устройство** для имитированного устройства **Подключенный кондиционер 1**, созданного в предыдущем руководстве:

![Устройство, которое активировало сообщение с уведомлением](media/tutorial-monitor-devices-experimental/sourcedevice.png)

## <a name="investigate-an-issue"></a>Изучение проблемы

Являясь оператором, вы можете просмотреть информацию об устройстве на страницах **Measurements** (Измерения), **Параметры**, **Свойства**, **Правила** и **Панель мониторинга**. Конструктор настроил в **панели мониторинга** отображение важной информации о подключенном кондиционере.

Выберите представление **Панель мониторинга**, чтобы просмотреть информацию об устройстве.

![Панель мониторинга устройства](media/tutorial-monitor-devices-experimental/initial_screen.png)

На панели мониторинга показан график температуры устройства. Вы также можете увидеть текущую целевую температуру устройства на плитке **Set target temperature** (Установить целевую температуру). Вы решили, что температура слишком велика.

## <a name="remediate-an-issue"></a>Устранение проблемы

Чтобы изменить целевую температуру устройства, на странице **Параметры**:

1. Выберите **Параметры**. Измените значение в поле **Set Temperature** (Установить температуру) на 75. Выберите **Обновить**, чтобы отправить новую целевую температуру на устройство. Когда устройство подтверждает изменение параметра, состояние параметра изменяется на **синхронизировано**:

    ![Обновление параметров](media/tutorial-monitor-devices-experimental/change_settings.png)

2. Выберите **Панель мониторинга** и проверьте новое значение параметра:

    ![Обновленная панель мониторинга устройства](media/tutorial-monitor-devices-experimental/new_settings.png)

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="nextstepaction"]
> * Получение уведомления
> * изучать проблему;
> * Устранение проблемы

Теперь, когда вы знаете, как отслеживать устройства, попробуйте [добавить устройство](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).