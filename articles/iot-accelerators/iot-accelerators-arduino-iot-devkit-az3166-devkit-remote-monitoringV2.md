---
title: 'IoT DevKit в облаке: подключение платы IoT DevKit AZ3166 к акселератору решений Интернета вещей для удаленного мониторинга | Документация Майкрософт'
description: В этом руководстве вы узнаете, как отправлять данные о состоянии датчиков IoT DevKit AZ3166 в акселератор решений Интернета вещей для удаленного мониторинга и визуализации.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: isacabe
ms.openlocfilehash: 32742b2a680370f443051e2d86f90d94e8632850
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720588"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Подключение MXChip IoT DevKit AZ3166 к акселератору решений для удаленного мониторинга Интернета вещей

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Вы узнаете, как запустить пример приложения с помощью своего набора разработки, чтобы обеспечить отправку данных датчиков в акселератор решений.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) — это универсальная совместимая с Arduino плата со множеством периферийных устройств и датчиков. Вы можете выполнить для нее разработку с помощью [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) в Visual Studio Code. Она предоставляется с расширяющимся [каталогом проектом](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/), чтобы помочь вам выполнить прототипирование решений Интернета вещей, использующих службы Microsoft Azure.

## <a name="what-you-need"></a>Необходимые элементы

Пройдите [руководство по началу работы](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) и **выполните только следующие разделы**:

* Подготовка оборудования
* Настройка Wi-Fi
* Начало использования платы DevKit
* Подготовка среды разработки

## <a name="open-the-remote-monitoring-sample-in-vs-code"></a>Откройте пример удаленного мониторинга в VS Code

1. Убедитесь, что плата IoT DevKit **не подключена** к компьютеру. Сначала запустите VS Code, а затем подключите плату DevKit к компьютеру.

2. Нажмите клавишу `F1`, чтобы открыть палитру команд, потом введите и выберите **IoT Workbench: Examples** (Примеры IoT Workbench). Затем выберите **IoT DevKit** в качестве платы.

3. Найдите **Удаленный мониторинг** и нажмите **Открыть пример**. Откроется новое окно VSCode с папкой проекта.
  ![IoT Workbench, выберите пример удаленного мониторинга](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-iot-hub-device-connection-string"></a>Настройка строки подключения к устройству центра Интернета вещей

1. Переведите IoT DevKit в **режим настройки**. Для этого выполните следующие действия:
   * Нажмите и удерживайте кнопку **A**.
   * Нажмите и отпустите кнопку **сброса**.

2. На экране появится идентификатор DevKit и надпись "Configuration" (Настройка).
   
  ![Режим настройки IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

3. Нажмите клавишу `F1`, чтобы открыть палитру команд, потом введите и выберите **IoT Workbench: Устройство > Настроить параметры устройства**.

4. Вставьте только что скопированную строку подключения и нажмите `Enter` для ее настройки.

## <a name="build-and-upload-the-device-code"></a>Создание и передача кода устройства

1. Нажмите клавишу `F1`, чтобы открыть палитру команд, потом введите и выберите **IoT Workbench: Устройство > Загрузка устройства**.
  ![IoT Workbench: Устройство - > Загрузка](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. VS Code запускает компиляцию и загрузку кода на в DevKit.
  ![IoT Workbench: Устройство - > Загружено](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

Плата DevKit перезагрузится и начнет выполнение кода.

## <a name="test-the-project"></a>Тестирование проекта

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Просмотр данных телеметрии, отправленных в решение для удаленного мониторинга

При запуске примера приложения DevKit отправляет данные от датчиков по сети Wi-Fi в решение для удаленного мониторинга. Чтобы просмотреть результат, сделайте следующее:

1. Перейдите на панель мониторинга решения и щелкните **Устройства**.

2. Щелкните имя устройства на вкладке справа, отобразится состояние датчиков DevKit в режиме реального времени.
  ![Данные датчиков в Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="send-a-c2d-message"></a>Отправка сообщения C2D

Решение для удаленного мониторинга позволяет использовать удаленный метод на устройстве. В примере кода содержатся три метода, которые можно увидеть в разделе **Метод**, выбрав датчик.

![Методы IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Давайте попробуем изменить цвет индикаторов DevKit с помощью метода LedColor.

1. Выберите имя устройства из списка устройств и щелкните **Задания**.

  ![Создание задания](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

2. Настройте задания, как показано ниже, и нажмите **Применить**.
  * Выберите задание: **Метод запуска**
  * Имя метода: **LedColor**
  * Имя задания: **ChangeLedColor**
  
  ![Параметры задания](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

Через несколько секунд DevKit изменит цвет индикатора RGB (под клавишей А).

![Красный индикатор IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете перейти к следующим руководствам, оставьте акселератор решений для удаленного мониторинга развернутым.

Если вам больше не нужен ускоритель решения, удалите его со страницы "Подготовленные решения", а затем щелкните "Удалить решение":

![Удаление решения](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Если вы столкнулись с проблемами, ознакомьтесь с [вопросами и ответами об IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) или используйте один из следующих каналов связи:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Дополнительная информация

Теперь вы знаете, как подключить устройство DevKit к акселератору решений для удаленного мониторинга Интернета вещей Azure и визуализировать данные датчиков. Мы предлагаем вам следующие шаги для дальнейшего обучения:

* [Обзор акселераторов решений для Интернета вещей Azure](https://docs.microsoft.com/azure/iot-suite/)
* [Настройка пользовательского интерфейса](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
* [Подключение IoT DevKit к приложению Azure IoT Central](../iot-central/howto-connect-devkit.md)