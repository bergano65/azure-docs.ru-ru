---
title: Подключение устройства DevKit к приложению Azure IoT Central | Документация Майкрософт
description: Вы узнаете, как разработчик устройства может подключить устройство MXChip IoT DevKit к приложению Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 270f92365823fb0f9378a9daae77dbbe08b53b14
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435086"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Подключение устройства MXChip IoT DevKit к приложению Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

В этой статье описано, каким образом разработчик устройства может подключить устройство MXChip IoT DevKit (DevKit) к приложению Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Перед началом работы

Для выполнения действий, описанных в этой статье, необходимы следующие ресурсы:

1. Приложение IoT Central Azure, созданное из шаблона приложения **устаревшего** приложения. Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).
1. Устройство DevKit. Чтобы приобрести устройство DevKit, посетите сайт [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="add-a-device-template"></a>Добавление шаблона устройства

В приложении IoT Central Azure добавьте новый шаблон устройства **MXChip** , который определяет следующие характеристики устройства:

- Измерения телеметрии для **влажности**, **температуры**, **давления**, **магнитометр** (измеряемая по осям x, y, оси z), **акселерометр** (измеряется вдоль x, y, оси z) и **гироскопом** (измеряется по осям x, y, оси z).
- Измерение состояний для **состояния устройства**.
- Измерение событий для **нажатой кнопки б**.
- Параметры для **напряжения**, **тока**, **скорости вращения вентилятора**и выключателя **IR** .
- Свойства устройства **номер кристалла** и **расположение устройства**, которое является свойством расположения.
- Облачное свойство, **изготовленное в**.
- Команды **echo** и **Обратный отсчет**. Когда реальное устройство получает команду **echo** , оно показывает отправленное значение на экране устройства. Когда реальное устройство получает команду **обратного отсчета** , индикатор циклически проходит через шаблон, и устройство отправляет значения обратного отсчета обратно в IOT Central.

1. Выберите **+ создать** на основе шаблонов устройств ![шаблон устройства](media/howto-connect-devkit/adddevicetemplate.png)
   

2. Выберите **MXChip** и создайте шаблон устройства MXChip ![добавить шаблон устройства](media/howto-connect-devkit/newtemplate.png)

Подробные сведения о конфигурации см. в разделе [сведения о шаблоне устройства MXChip](#mxchip-device-template-details) .

## <a name="add-a-real-device"></a>Добавление реального устройства

### <a name="get-your-device-connection-details"></a>Получение сведений о подключении устройства

В приложении IoT Central Azure добавьте реальное устройство из шаблона устройства **MXChip** и запишите сведения о подключении устройства: **идентификатор области, идентификатор устройства и первичный ключ**:

1. Добавьте **реальное устройство** с устройств, выберите **+ New > вещественное** , чтобы добавить реальное устройство.

    * Введите **идентификатор устройства**в нижнем регистре или используйте предлагаемый **идентификатор устройства**.
    * Введите **имя устройства**или используйте предложенное имя

    ![Add Device (Добавление устройства)](media/howto-connect-devkit/add-device.png)

1. Чтобы получить сведения о подключении устройства, **идентификатор области**, **идентификатор устройства**и **первичный ключ**, на странице устройства выберите **подключить** .

    ![Сведения о подключении](media/howto-connect-devkit/device-connect.png)

1. Запишите сведения о подключении. Вы временно отключитесь от Интернета при подготовке устройства DevKit на следующем шаге.

### <a name="prepare-the-devkit-device"></a>Подготовка устройства DevKit

Если вы ранее использовали устройство и хотите перенастроить его для использования другой сети WiFi, строки подключения или измерения телеметрии, одновременно нажмите кнопки **a** и **B** . Если это не поможет, нажмите кнопку " **сбросить** " и повторите попытку.

#### <a name="to-prepare-the-devkit-device"></a>Чтобы подготовить устройство DevKit, сделайте следующее:

1. Скачайте последнюю версию встроенного ПО Azure IoT Central для MXChip на [этой](https://aka.ms/iotcentral-docs-MXChip-releases) странице на сайте GitHub.
1. Подключите устройство DevKit к компьютеру, на котором будет выполняться разработка, с помощью USB-кабеля. В Windows в проводнике откроется диск, сопоставленный с хранилищем на устройстве DevKit. Например, этот диск может называться **AZ3166 (D:)** .
1. Перетащите файл **iotCentral.bin** в окно диска. По завершении копирования устройство перезагрузится с новой версией встроенного ПО.

1. При перезапуске устройства DevKit отображается следующий экран:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Если на экране отображается что-либо еще, сбросьте устройство и нажмите одновременно кнопки **A** и **B** на устройстве, чтобы перезагрузить его.

1. Устройство находится в режиме точки доступа. Вы можете подключаться к точке доступа Wi-Fi с компьютера или мобильного устройства.

1. На компьютере, телефоне или планшете подключитесь к сети Wi-Fi с именем, отображаемым на экране устройства. При подключении к этой сети у вас нет доступа к Интернету. Это состояние является ожидаемым, и вы подключаетесь к этой сети только в течение короткого времени при настройке устройства.

1. Откройте веб-браузер и перейдите по адресу [http://192.168.0.1/start](http://192.168.0.1/start). Откроется следующая веб-страница:

    ![Страница конфигурации устройства](media/howto-connect-devkit/configpage.png)

    На веб-странице введите:
    - Имя сети Wi-Fi
    - Пароль сети Wi-Fi
    - ПИН-код, отображаемый на экране устройства
    - **Идентификатор области**сведений о подключении, **идентификатор устройства**и **первичный ключ** устройства (вы уже должны были сохранить это, выполнив указанные ниже действия).
    - Выбрать все доступные измерения телеметрии

1. Если выбрать **Настройка устройства**, откроется следующая страница:

    ![Устройство настроено](media/howto-connect-devkit/deviceconfigured.png)

1. Нажмите кнопку **Reset** (Сбросить) на устройстве.

## <a name="view-the-telemetry"></a>Просмотр телеметрии

При перезапуске устройства DevKit на экране устройства можно увидеть следующее:

* количество отправленных сообщений телеметрии;
* количество сбоев;
* количество полученных требуемых свойств и отправленных передаваемых свойств.

> [!NOTE]
> Если устройство кажется циклическим при попытке подключения, проверьте, не **заблокировано** ли устройство в IOT Central, и **Разблокируйте** устройство, чтобы оно могла подключиться к приложению.

Встряхните устройство для отправки сообщаемого свойства. Устройство отправляет случайное число в качестве свойства устройства **Серийный номер**.

Вы можете просмотреть измерения телеметрии и значения передаваемых свойств, а также настроить параметры в Azure IoT Central:

1. Используйте **устройства** для перехода на страницу " **измерения** " для реального устройства MXChip, которое вы добавили:

    ![Переход к реальному устройству](media/howto-connect-devkit/realdevicenew.png)

1. На странице **Measurements** (Измерения) можно просмотреть данные телеметрии, поступающие из устройства MXChip.

    ![Просмотр данных телеметрии из реального устройства](media/howto-connect-devkit/devicetelemetrynew.png)

1. На странице **Свойства** можно просмотреть последний серийный номер и сведения о расположении устройства, переданные устройством:

    ![Просмотр свойств устройства](media/howto-connect-devkit/devicepropertynew.png)

1. На странице **Параметры** можно обновить параметры устройства MXChip:

    ![Просмотр параметров устройства](media/howto-connect-devkit/devicesettingsnew.png)

1. На странице **команды** можно вызвать команды **echo** и **Обратный отсчет** :

    ![Вызвать команды](media/howto-connect-devkit/devicecommands.png)

1. На странице **Панель мониторинга** вы увидите карту расположения.

    ![Просмотр панели мониторинга устройства](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Скачивание исходного кода

Если вы хотите просмотреть и изменить код устройства, его можно скачать с сайта GitHub. Если вы планируете изменить код, необходимо следовать этим инструкциям по [подготовке среды разработки](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) для операционной системы настольных устройств.

Чтобы скачать исходный код, выполните следующую команду на настольном компьютере:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Исходный код будет скачан в папку с именем `iot-central-firmware`.

> [!NOTE]
> Если **git** не установлен в среде разработки, его можно скачать здесь: [https://git-scm.com/download](https://git-scm.com/download).

## <a name="review-the-code"></a>Просмотр кода

Используйте Visual Studio Code, чтобы открыть папку `MXCHIP/mxchip_advanced` в папке `iot-central-firmware`:

![Код Visual Studio](media/howto-connect-devkit/vscodeview.png)

Чтобы увидеть, как данные телеметрии отправляются в приложение IoT Central Azure, откройте файл **телеметрии. cpp** в папке `src`.

- Функция `TelemetryController::buildTelemetryPayload` создает полезные данные телеметрии JSON, используя данные из датчиков устройства.

- Функция `TelemetryController::sendTelemetryPayload` вызывает `sendTelemetry` в **азуреиотклиент. cpp** для отправки полезных данных JSON в центр Интернета вещей, используемый приложением Azure IOT Central.

Чтобы увидеть, как значения свойств передаются в приложение IoT Central Azure, откройте файл **телеметрии. cpp** в папке `src`.

- Функция `TelemetryController::loop` отправляет свойство **Location** в отчете примерно каждые 30 секунд. В нем используется функция `sendReportedProperty` в исходном файле **азуреиотклиент. cpp** .

- Функция `TelemetryController::loop` отправляет свойство сообщила о **диенумбер** , когда акселерометр устройства обнаруживает двойное касание. В нем используется функция `sendReportedProperty` в исходном файле **азуреиотклиент. cpp** .

Чтобы увидеть, как устройство реагирует на команды, вызываемые из IoT Central приложения, откройте файл **регистередмесодхандлерс. cpp** в папке `src`:

- Функция **дмечо** является обработчиком для команды **echo** . Он показывает **дисплайедвалуе** в полезных данных на экране устройства.

- Функция **дмкаунтдовн** — это обработчик для команды **обратного отсчет** . Он изменяет цвет индикатора устройства и использует сообщаемое свойство для отправки значения обратного отсчета обратно в приложение IoT Central. Сообщаемое свойство имеет то же имя, что и команда. Функция использует функцию `sendReportedProperty` в исходном файле **азуреиотклиент. cpp** .

Код в исходном файле **азуреиотклиент. cpp** использует функции из [пакетов sdk и библиотек Microsoft Azure IOT для C](https://github.com/Azure/azure-iot-sdk-c) для взаимодействия с центром Интернета вещей.

Сведения о способах изменения, создания и отправки примера кода на устройство, см. в файле **readme.md** в папке `MXCHIP/mxchip_advanced`.

## <a name="mxchip-device-template-details"></a>Сведения о шаблоне устройства MXChip

Приложение, созданное из шаблона приложения примера Devkits, включает в себя шаблон устройства MXChip со следующими характеристиками:

### <a name="measurements"></a>Измерения

#### <a name="telemetry"></a>Телеметрия

| Имя поля     | Единицы  | Минимальные | Максимальная | Число десятичных знаков |
| -------------- | ------ | ------- | ------- | -------------- |
| Влажность       | %      | 0       | 100     | 0              |
| temp           | °C     | –40     | 120     | 0              |
| pressure       | гПа    | 260     | 1260    | 0              |
| magnetometerX  | мГс | –1000   | 1000    | 0              |
| magnetometerY  | мГс | –1000   | 1000    | 0              |
| magnetometerZ  | мГс | –1000   | 1000    | 0              |
| accelerometerX | mg     | –2000   | 2000    | 0              |
| accelerometerY | mg     | –2000   | 2000    | 0              |
| accelerometerZ | mg     | –2000   | 2000    | 0              |
| gyroscopeX     | милиградусов/с   | –2000   | 2000    | 0              |
| gyroscopeY     | милиградусов/с   | –2000   | 2000    | 0              |
| gyroscopeZ     | милиградусов/с   | –2000   | 2000    | 0              |

#### <a name="states"></a>Состояния 
| Имя          | Отображаемое имя   | NORMAL | ВНИМАНИЕ! | ОПАСНОСТЬ! | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Состояние устройства   | Зеленый  | Orange  | Красный    | 

#### <a name="events"></a>Мероприятия 
| Имя             | Отображаемое имя      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Нажата кнопка B  | 

### <a name="settings"></a>Настройки

Числовые параметры

| Отображаемое имя | Имя поля | Единицы | Число десятичных знаков | Минимальные | Максимальная | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Напряжение      | setVoltage | В | 0              | 0       | 240     | 0       |
| Текущий      | setCurrent | Амперы  | 0              | 0       | 100     | 0       |
| Скорость вращения вентилятора    | fanSpeed   | Об/мин   | 0              | 0       | 1000    | 0       |

Параметры переключения

| Отображаемое имя | Имя поля | Включение текста | Отключение текста | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | Выключено     |

### <a name="properties"></a>Свойства

| Тип            | Отображаемое имя | Имя поля | Тип данных |
| --------------- | ------------ | ---------- | --------- |
| Свойство устройства | Серийный номер   | dieNumber  | number    |
| Свойство устройства | Расположение устройства   | location  | location    |
| Текст            | Произведено в     | manufacturedIn   | Н/Д       |

### <a name="commands"></a>Команды

| Отображаемое имя | Имя поля | Возвращаемый тип | Отображаемое имя поля ввода | Имя входного поля | Тип входного поля |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Echo         | echo (вывод на экран)       | text        | отображаемое значение         | дисплайедвалуе   | text             |
| Оставшего    | оставшего  | number      | Число из               | каунтфром        | number           |

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как подключить MXChip IoT DevKit к приложению Azure IoT Central, предлагаем следующий шаг, чтобы узнать, как [настроить пользовательский шаблон устройства](howto-set-up-template.md) для вашего устройства IOT.
