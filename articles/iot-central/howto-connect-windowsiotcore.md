---
title: Подключение устройства Windows IoT Core к приложению Azure IoT Central | Документация Майкрософт
description: Вы узнаете, как разработчик устройства может подключить устройство MXChip IoT DevKit к приложению Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: af6d66d2e3eae80477a151323578b930dcd7727a
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617858"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Подключение устройства Windows IoT Core к приложению Azure IoT Central

В этой статье описано, каким образом разработчик устройства может подключить устройство Windows IoT Core к приложению Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

- Приложение Azure IoT Central, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits). Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).

- Устройство под управлением операционной системы Windows 10 IoT Core. Дополнительные сведения см. в разделе [настраивается устройство Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Компьютер для разработки с [Node.js](https://nodejs.org/) версии 8.0.0 или более поздней версии. Вы можете запустить `node --version` в командной строке, чтобы проверить версию. Node.js доступен для разных операционных систем.

## <a name="the-sample-devkits-application"></a>Devkits примера приложения

Приложение, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits), включает в себя шаблон приложения **Windows IoT Core** со следующими характеристиками:

- Измерения данные телеметрии для устройства: **Влажности**, **температуры**, и **давление**.
- Параметр, чтобы разрешить **скорость вентилятора**.
- Свойства устройства **Die номера** и свойства облачной **расположение**.

Подробные сведения о конфигурации шаблона устройства см. в разделе [сведения о шаблоне Windows IoT Core Device](#device-template-details).

## <a name="add-a-real-device"></a>Добавление реального устройства

В приложении Azure IoT Central использовать **Device Explorer** страницу, чтобы добавить реального устройства из **Windows 10 IoT Core** шаблон устройства. Запомните или запишите устройства сведения о подключении (**идентификатор области**, **идентификатор устройства**, и **первичный ключ**). Дополнительные сведения см. в разделе [получение сведений о подключении](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Подготовка устройства

Для устройства для подключения к IoT Central ему строку подключения.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

Код устройства, для доступа к строке подключения, сохраните его в файл с именем **connection.string.iothub** в папке `C:\Data\Users\DefaultAccount\Documents\` на устройстве Windows 10 IoT Core.

Чтобы скопировать **connection.string.iothub** файл из на настольном компьютере `C:\Data\Users\DefaultAccount\Documents\` папку на вашем устройстве, можно использовать [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Используйте веб-браузере для перехода к Windows Device Portal на вашем устройстве.
1. Для работы с файлами на вашем устройстве, выберите **приложений > проводнике**.
1. Перейдите к **Folders\Documents пользователя**. Затем отправьте **connection.string.iothub** файла:

    ![Передать строку подключения](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Развертывание и запуск

Чтобы развернуть и запустить пример приложения на устройстве, можно использовать [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Используйте веб-браузере для перехода к Windows Device Portal на вашем устройстве.
1. Для развертывания и запуска **клиента центра Интернета вещей Azure** приложения, выберите **приложений > примеры быстрого запуска**. Затем выберите **клиента центра Интернета вещей Azure**.
1. Затем выберите **развертывание и запуск**.

    ![Развертывание и запуск](media/howto-connect-windowsiotcore/quick-run.png)

Через несколько минут можно просмотреть данные телеметрии с устройства в приложении IoT Central.

[Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) включает средства, которые можно использовать для устранения неполадок устройства:

- **Apps manager** страница предназначена для управления приложениями, установленной на устройстве.
- Если у вас нет монитор, подключенный к устройству, можно использовать **параметры устройства** страницы, чтобы сделать снимки экрана с устройства. Например: 

    ![Снимок экрана приложения](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Скачивание исходного кода

Если вы хотите просмотреть и изменить исходный код для клиентского приложения, его можно скачать из [репозиторий GitHub для Windows-iotcore-samples](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Сведения о шаблоне устройства

Приложение, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits), включает в себя шаблон приложения **Windows IoT Core** со следующими характеристиками:

### <a name="telemetry-measurements"></a>Измерения телеметрии

| Имя поля     | Units  | Минимальная | Максимальная | Число десятичных знаков |
| -------------- | ------ | ------- | ------- | -------------- |
| Влажность       | %      | 0       | 100     | 0              |
| temp           | °C     | –40     | 120     | 0              |
| pressure       | гПа    | 260     | 1260    | 0              |

### <a name="settings"></a>Параметры

Числовые параметры

| Отображаемое имя | Имя поля | Units | Число десятичных знаков | Минимальная | Максимальная | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Скорость вращения вентилятора    | fanSpeed   | Об/мин   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>properties

| type            | Отображаемое имя | Имя поля | Тип данных |
| --------------- | ------------ | ---------- | --------- |
| Свойство устройства | Серийный номер   | dieNumber  | number    |
| текст            | Расположение     | location   | Н/Д       |

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как для подключения устройства Raspberry Pi к Azure IoT Central приложения, мы предлагаем — Узнайте, как [настроить шаблон пользовательского устройства](howto-set-up-template.md) для устройства Интернета вещей.