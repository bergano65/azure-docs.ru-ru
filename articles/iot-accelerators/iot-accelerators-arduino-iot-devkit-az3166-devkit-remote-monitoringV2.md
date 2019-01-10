---
title: Подключение IoT DevKit к акселератору решений удаленного мониторинга (Azure) | Документация Майкрософт
description: В этом практическом руководстве вы узнаете, как отправлять данные телеметрии с датчиков IoT DevKit AZ3166 в акселератор решений удаленного мониторинга для их мониторинга и визуализации.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: eb2a6692c0b00dc4419c601228453a8cfc44c02a
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156788"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Подключение устройства IoT DevKit к акселератору решений удаленного мониторинга

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

В этом практическом руководстве показано, как запустить пример приложения на устройстве IoT DevKit. Этот пример кода отправляет данные телеметрии с датчиков устройства DevKit в акселератор решений.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) — это универсальная совместимая с Arduino плата со множеством периферийных устройств и датчиков. Решения для нее можно разрабатывать с помощью [Azure IoT Device Workbench](https://aka.ms/iot-workbench) или пакета расширений [Инструменты Azure IoT](https://aka.ms/azure-iot-tools) в Visual Studio Code. [Каталог проектов](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) содержит образцы приложений, которые помогут создать прототип решения для Интернета вещей.

## <a name="before-you-begin"></a>Перед началом работы

Для работы с этим руководством сначала следует выполнить следующие задачи:

* Подготовьте DevKit по инструкциям из статьи [Подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure в облаке](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="open-sample-project"></a>Открытие примера проекта

Чтобы открыть пример удаленного мониторинга в VS Code, выполните следующее.

1. Убедитесь, что плата IoT DevKit не подключена к компьютеру. Сначала запустите VS Code, а затем подключите плату DevKit к компьютеру.

1. Щелкните `F1`, чтобы открыть палитру команд, затем введите и выберите **Azure IoT Device Workbench. Открыть примеры...**. Затем выберите **IoT DevKit** в качестве платы.

1. Найдите **Удаленный мониторинг** и нажмите **Открыть пример**. Откроется новое окно VS Code с содержимым папки проекта.

  ![IoT Workbench, выберите пример удаленного мониторинга](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Настройка устройства

Чтобы настроить на устройстве DevKit строку подключения устройства к Центру Интернета вещей, выполните следующее.

1. Переведите IoT DevKit в **режим настройки**.

    * Нажмите и удерживайте кнопку **A**.
    * Нажмите и отпустите кнопку **сброса**.

1. На экране появится идентификатор DevKit и надпись `Configuration`.

    ![Режим настройки IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Нажмите клавишу **F1**, чтобы открыть палитру команд, затем введите и выберите **Azure IoT Device Workbench. Настройка параметров устройства… > Настройка строки подключения к устройству**.

1. Вставьте скопированную ранее строку подключения и нажмите клавишу **ввод**, чтобы настроить устройство.

## <a name="build-the-code"></a>Сборка кода

Чтобы скомпилировать и передать код устройства, выполните следующее.

1. Нажмите `F1`, чтобы открыть палитру команд, затем введите и выберите **Azure IoT Device Workbench. Отправка кода устройства**.

1. VS Code скомпилирует код и передаст его на устройство DevKit.

    ![IoT Workbench. Устройство - > Отправлено](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. Устройство DevKit перезагрузится и выполнит загруженный код.

## <a name="test-the-sample"></a>Тестирование примера

Чтобы убедиться в работоспособности примера, который вы загрузили в устройство DevKit, выполните следующее.

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Просмотр данных телеметрии, отправленных в решение для удаленного мониторинга

При запуске примера приложения устройство DevKit отправляет по Wi-Fi данные телеметрии со своих датчиков в акселератор решений. Чтобы просмотреть эти данные телеметрии, выполните следующее.

1. Перейдите на панель мониторинга решения и щелкните **Устройства**.

1. Щелкните имя нужного устройства DevKit. На вкладке справа вы увидите данные телеметрии, поступающие из DevKit, в режиме реального времени.

    ![Данные датчиков в Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Управление устройством DevKit

Акселератор решений удаленного мониторинга позволяет дистанционно управлять устройством. В нашем примере кода реализованы три метода, которые вы найдете в разделе **Метод**, выбрав это устройство на странице **Устройства**.

![Методы IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Чтобы изменить цвет одного из индикаторов DevKit, используйте метод **LedColor**.

1. Выберите имя устройства из списка устройств и щелкните **Задания**.

    ![Создание задания](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Настройте задания, указав следующие значения, и щелкните **Применить**:

    * Выберите задание: **Метод запуска**
    * Имя метода: **LedColor**
    * Имя задания: **ChangeLedColor**

    ![Параметры задания](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Через несколько секунд изменится цвет индикатора RGB (под клавишей А) на устройстве DevKit.

    ![Красный индикатор IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете перейти к следующим руководствам, оставьте акселератор решений для удаленного мониторинга развернутым.

Если вам больше не нужен ускоритель решения, удалите его со страницы "Подготовленные решения", а затем щелкните "Удалить решение":

![Удаление решения](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Если вы столкнулись с проблемами, ознакомьтесь с [вопросами и ответами об IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) или используйте один из следующих каналов для связи с нами:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Дополнительная информация

Теперь вы знаете, как подключить устройство DevKit к акселератору решений удаленного мониторинга. Выполните следующие шаги в рамках дальнейшего обучения:

* [Обзор акселераторов решений для Интернета вещей Azure](https://docs.microsoft.com/azure/iot-accelerators/)
* [Настройка пользовательского интерфейса](iot-accelerators-remote-monitoring-customize.md)
* [Подключение IoT DevKit к приложению Azure IoT Central](../iot-central/howto-connect-devkit.md)