---
title: Подключение устройства Windows IoT Core к приложению Azure IoT Central | Документация Майкрософт
description: Вы узнаете, как разработчик устройства может подключить устройство MXChip IoT DevKit к приложению Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d880130e114b2bd3e4f978c2ae3fc7bacf0648c4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953906"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Подключение устройства Windows IoT Core к приложению Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

В этой статье описано, каким образом разработчик устройства может подключить устройство Windows IoT Core к приложению Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

- Приложение Azure IoT Central, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits). Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).

- Устройство под управлением операционной системы Windows 10 IoT Core. Дополнительные сведения см. в разделе [Настройка устройства Windows 10 IOT базовая](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Компьютер разработки с [node. js](https://nodejs.org/) версии 8.0.0 или более поздней. Вы можете запустить `node --version` в командной строке, чтобы проверить версию. Node.js доступен для разных операционных систем.

## <a name="the-sample-devkits-application"></a>Пример приложения Девкитс

Приложение, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits), включает в себя шаблон приложения **Windows IoT Core** со следующими характеристиками:

- Измерения телеметрии для устройства: **влажность**, **температура**и **давление**.
- Настройка управления **скоростью вентилятора**.
- **Номер кристалла** свойства устройства и **Расположение**свойства облака.

Подробные сведения о конфигурации шаблона устройства см. в разделе [сведения о шаблоне устройства Windows IOT базовая](#device-template-details).

## <a name="add-a-real-device"></a>Добавление реального устройства

В приложении IoT Central Azure используйте страницу **Device Explorer** , чтобы добавить реальное устройство из шаблона устройства **Windows 10 IOT базовая** . Запишите сведения о подключении устройства (**идентификатор области**, **идентификатор устройства**и **первичный ключ**).

## <a name="prepare-the-device"></a>Подготовка устройства

Для подключения устройства к IoT Central требуется строка подключения:

1. Создайте строку подключения с помощью служебной программы командной строки `dps-keygen`:

    Чтобы установить [служебную программу генератора ключей](https://github.com/Azure/dps-keygen), выполните следующую команду:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Чтобы создать строку подключения, выполните следующую команду, используя сведения о подключении, которые вы записали ранее:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Скопируйте строку подключения в выходных данных `dps-keygen`, чтобы использовать ее в коде устройства.

Чтобы код устройства может получить доступ к строке подключения, сохраните его в файле **Connection. String. iothub** в папке `C:\Data\Users\DefaultAccount\Documents\` на устройстве Windows 10 IOT базовая.

Чтобы скопировать файл **Connection. String. iothub** с настольного компьютера в папку `C:\Data\Users\DefaultAccount\Documents\` на устройстве, можно использовать [портал устройств Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Используйте веб-браузер для перехода на портал устройств Windows на устройстве.
1. Чтобы просмотреть файлы на устройстве, выберите **приложения > проводнике**.
1. Перейдите к **пользователю фолдерс\документс**. Затем отправьте файл **Connection. String. iothub** :

    ![Отправка строки подключения](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Развертывание и запуск

Чтобы развернуть и запустить пример приложения на устройстве, можно использовать [портал устройств Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Используйте веб-браузер для перехода на портал устройств Windows на устройстве.
1. Чтобы развернуть и запустить **клиентское приложение центра Интернета вещей Azure** , выберите **приложения > примеры для быстрого запуска**. Затем выберите **клиент центра Интернета вещей Azure**.
1. Затем выберите **развернуть и запустить**.

    ![Развертывание и запуск](media/howto-connect-windowsiotcore/quick-run.png)

Через пару минут вы сможете просмотреть данные телеметрии с устройства в приложении IoT Central.

[Портал устройств Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) содержит средства, которые можно использовать для устранения неполадок устройства:

- Страница **Диспетчер приложений** позволяет управлять приложениями, выполняемыми на устройстве.
- Если у вас нет монитора, подключенного к устройству, можно использовать страницу **Параметры устройства** для записи снимков экрана с устройства. Пример.

    ![Снимок экрана приложения](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Скачивание исходного кода

Если вы хотите исследовать и изменить исходный код клиентского приложения, его можно скачать из [репозитория GitHub Windows-иоткоре-Samples](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Сведения о шаблоне устройства

Приложение, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits), включает в себя шаблон приложения **Windows IoT Core** со следующими характеристиками:

### <a name="telemetry-measurements"></a>Измерения телеметрии

| Имя поля     | Единицы  | Минимальная | Максимальная | Число десятичных знаков |
| -------------- | ------ | ------- | ------- | -------------- |
| Влажность       | %      | 0       | 100     | 0              |
| temp           | °C     | –40     | 120     | 0              |
| pressure       | гПа    | 260     | 1260    | 0              |

### <a name="settings"></a>Настройки

Числовые параметры

| Отображаемое имя | Имя поля | Единицы | Число десятичных знаков | Минимальная | Максимальная | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Скорость вращения вентилятора    | fanSpeed   | Об/мин   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>properties

| Тип            | Отображаемое имя | Имя поля | Тип данных |
| --------------- | ------------ | ---------- | --------- |
| Свойство устройства | Серийный номер   | dieNumber  | number    |
| Текст            | Location     | location   | Н/Д       |

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как подключить устройство Windows IoT Core к приложению Azure IoT Central, предлагаем следующий шаг: Узнайте, как [настроить пользовательский шаблон устройства](howto-set-up-template.md) для вашего устройства IOT.
