---
title: включить файл
description: включить файл
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 28f676892967abbd0da63d7a75ea3d164b87ce97
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017516"
---
Оператор приложения Azure IoT Central может выполнять следующие действия:

* Данные телеметрии, переданные устройством, можно просмотреть на странице **Overview** (Обзор).

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Просмотр телеметрии устройства":::

* На странице **About** (Сведения) можно просмотреть свойства устройства.

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Просмотр свойств устройства":::

## <a name="customize-the-device-template"></a>Настройка шаблона устройства

Разработчик решения может настроить шаблон устройства, который автоматически создан IoT Central при подключении устройства термостата.

Чтобы добавить свойство облака для хранения имени клиента, связанного с устройством, выполните следующие действия.

1. В приложении IoT Central перейдите к шаблону устройства **термостата** на странице **Шаблоны устройств**.

1. В шаблоне устройства **термостата** выберите **Свойства облака**.

1. Выберите команду **Добавить облачное свойство**. Введите *имя клиента* в качестве **отображаемого имени** и выберите **строку** в качестве **схемы**. Затем выберите **Сохранить**.

Чтобы настроить отображение команды **Get Max-Min report** в приложении IoT Central, выберите **Настройка** в шаблоне устройства. Замените команду **Get Max-Min report** на *Get status report*. Затем выберите **Сохранить**.

Модель **термостата** включает доступное для записи свойство **Target Temperature** (Целевая температура), а шаблон устройства — облачное свойство **Customer Name** (Имя клиента). Создайте представление, которое оператор может использовать для изменения этих свойств, выполнив следующие действия:

1. Выберите **Views** (Представления) и щелкните плитку **Editing device and cloud data** (Изменение данных об устройстве и облаке).

1. Введите имя формы _Properties_ (Свойства).

1. Выберите свойства **Target Temperature** (Целевая температура) и **Customer Name** (Имя клиента). Затем щелкните **Add section** (Добавить раздел).

1. Сохраните изменения.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Представление для обновления значений свойств":::

## <a name="publish-the-device-template"></a>Публикация шаблона устройства

Прежде чем оператор сможет просмотреть и использовать выполненные настройки, необходимо опубликовать шаблон устройства.

В меню шаблона устройства выберите устройство **термостата** и щелкните **Опубликовать**. На панели **Опубликовать этот шаблон устройства в приложении** щелкните **Опубликовать**.

Оператор теперь может использовать представление **Свойства** для обновления значений свойств и вызова команды **Get status report** на странице команд устройства:

* Измените значения доступных для записи свойств на странице **Properties** (Свойства).

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Обновление свойств устройства":::

* Выполните команды на странице **Команды**.

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Вызов команды":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Просмотр ответа команды":::
