---
title: Расширение Azure IoT Central с настраиваемыми правилами и уведомления | Документация Майкрософт
description: Разработчикам решений настройте для отправки уведомлений по электронной почте, когда устройство перестает отправлять данные телеметрии приложения IoT Central. Это решение использует Azure Stream Analytics и функций Azure.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 6140a8aea3fe0fe0a8f1c01cd1c97404c41f7a69
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65805986"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>Расширение центра Интернета вещей Azure с помощью настраиваемых правил, которые отправляют уведомления

В этом практическом руководстве показано, как разработчик решения, как можно расширить ваше приложение центра Интернета вещей с помощью настраиваемых правил и уведомления. В примере отправки уведомления оператору, когда устройство перестает отправлять данные телеметрии. Решение использует [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) запроса и определить, когда устройство прекращает отправку данных телеметрии. Задания Stream Analytics использует ["функции Azure"](https://docs.microsoft.com/azure/azure-functions/) отправить уведомление отправляется сообщение электронной почты с помощью [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

В этом практическом руководстве показано, как выходят IoT Central уже способов ее с помощью встроенных правил и действий.

В этом практическом руководстве вы узнаете, как:

* Данные телеметрии из приложения IoT Central с помощью Stream *непрерывный Экспорт данных*.
* Создайте запрос Stream Analytics, которое обнаруживает, когда устройство была остановлена, отправка данных.
* Отправьте уведомление по электронной почте, с помощью функций Azure и служб SendGrid.

## <a name="prerequisites"></a>Технические условия

Чтобы выполнить действия, описанные в этом руководстве, вам потребуется активная подписка Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

### <a name="iot-central-application"></a>Приложения центра Интернета вещей

Создайте приложение IoT Central из [Azure IoT Central — Мои приложения](https://aka.ms/iotcentral) страницы со следующими параметрами:

| Параметр | Value |
| ------- | ----- |
| План оплаты | С оплатой по мере использования |
| Шаблон приложения | Образец Contoso |
| Имя приложения | Примите имя по умолчанию или выберите другое имя |
| URL-адрес | Примите имя по умолчанию или выберите собственный уникальный префикс URL-адреса |
| Каталог | Клиент Azure Active Directory |
| Подписка Azure | Ваша подписка Azure. |
| Регион | Восточная часть США |

Примеры и снимки экрана в этой статье используется **восточной части США** регион. Выберите расположение рядом с вами и убедитесь, что вы создаете все ресурсы в одном регионе.

### <a name="resource-group"></a>Группа ресурсов

Используйте [портал Azure, чтобы создать группу ресурсов](https://portal.azure.com/#create/Microsoft.ResourceGroup) вызывается **DetectStoppedDevices** должен содержать другие ресурсы, создании. Создание ресурсов Azure в том же расположении, что приложения IoT Central.

### <a name="event-hubs-namespace"></a>Пространство имен Центров событий

Используйте [портал Azure, чтобы создать пространство имен концентраторов событий](https://portal.azure.com/#create/Microsoft.EventHub) со следующими параметрами:

| Параметр | Значение |
| ------- | ----- |
| ИМЯ    | Выберите имя пространства имен |
| Ценовая категория | базовая; |
| Подписка | Ваша подписка |
| Группа ресурсов | DetectStoppedDevices |
| Расположение | Восточная часть США |
| Единицы пропускной способности | 1 |

### <a name="stream-analytics-job"></a>Задание Stream Analytics

Используйте [портал Azure, чтобы создать задание Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) со следующими параметрами:

| Параметр | Значение |
| ------- | ----- |
| ИМЯ    | Выберите имя задания |
| Подписка | Ваша подписка |
| Группа ресурсов | DetectStoppedDevices |
| Расположение | Восточная часть США |
| Среда размещения | Облако |
| Единицы потоковой передачи | 3 |

### <a name="function-app"></a>Приложение-функция

Используйте [портал Azure, чтобы создать приложение-функцию](https://portal.azure.com/#create/Microsoft.FunctionApp) со следующими параметрами:

| Параметр | Value |
| ------- | ----- |
| Имя приложения    | Выберите имя приложения-функции |
| Подписка | Ваша подписка |
| Группа ресурсов | DetectStoppedDevices |
| ОС |  Windows |
| План размещения | План потребления |
| Расположение | Восточная часть США |
| Стек времени выполнения | .NET |
| Хранилище | Создать |

### <a name="sendgrid-account"></a>Учетной записи SendGrid

Используйте [порталу Azure для создания учетной записи SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) со следующими параметрами:

| Параметр | Значение |
| ------- | ----- |
| ИМЯ    | Выберите имя учетной записи SendGrid |
| Пароль | Создание пароля |
| Подписка | Ваша подписка |
| Группа ресурсов | DetectStoppedDevices |
| Ценовая категория | Бесплатный F1 |
| Контактные данные | Введите необходимую информацию |

Если вы создали все необходимые ресурсы, ваш **DetectStoppedDevices** группы ресурсов выглядит примерно так:

![Обнаружение группы ресурсов остановлена устройств](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Создание концентратора событий

Можно настроить приложения IoT Central непрерывный Экспорт данных телеметрии в концентратор событий. В этом разделе создайте концентратор событий для получения данных телеметрии из приложения IoT Central. Концентратор событий обеспечивает данные телеметрии к заданию Stream Analytics для обработки.

1. На портале Azure, перейдите к пространству имен концентраторов событий и выберите **+ концентратор событий**.
1. Имя концентратора событий **centralexport**и выберите **создать**.

Пространству имен концентраторов событий выглядит примерно так:

![Пространство имен Центров событий](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Получение ключа SendGrid API

Приложения-функции требуется ключ SendGrid API для отправки сообщений электронной почты. Чтобы создать ключ SendGrid API:

1. На портале Azure перейдите к учетной записи SendGrid. Затем выберите **управление** для доступа к вашей учетной записи SendGrid.
1. В учетной записи SendGrid, выберите **параметры**, затем **ключи API**. Выберите **Создание ключа API**:

    ![Создайте ключ SendGrid API](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. На **Создание ключа API** страницу, создайте ключ с именем **AzureFunctionAccess** с **полный доступ** разрешения.
1. Запомните или запишите ключ API, оно понадобится при настройке приложения-функции.

## <a name="define-the-function"></a>Определение функции

Это решение использует приложения функций Azure для отправки уведомлений по электронной почте при и задание Stream Analytics обнаруживает устройство остановлена. Создание приложения-функции:

1. На портале Azure перейдите к **службы приложений** в экземпляре **DetectStoppedDevices** группы ресурсов.
1. Выберите **+** для создания новой функции.
1. На **Выбор СРЕДЫ разработки** выберите **на портале** , а затем выберите **Продолжить**.
1. На **CREATE FUNCTION** выберите **веб-перехватчик + API** , а затем выберите **создать**.

Портал создает функцию по умолчанию с именем **HttpTrigger1**:

![Функции триггера HTTP по умолчанию](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Настройка привязки функций

Для отправки сообщений электронной почты с помощью SendGrid, необходимо настроить привязки для функции следующим образом:

1. Выберите **интегрировать**, выберите выходные данные **HTTP ($return)**, а затем выберите **удалить**.
1. Выберите **+ новые выходные данные**, затем выберите **SendGrid**, а затем выберите **выберите**. Выберите **установить** установить расширение SendGrid.
1. По завершении установки выберите **использовать возвращаемое значение функции**. Добавьте допустимый **адрес** для получения уведомлений по электронной почте.  Добавьте допустимый **с адреса** для использования в качестве отправителя электронной почты.
1. Выберите **новый** рядом с полем **параметр приложения ключ API SendGrid**. Введите **SendGridAPIKey** как ключ, а также ключ SendGrid API, записанным ранее значением. Щелкните **Создать**.
1. Выберите **Сохранить** сохранить привязки SendGrid для функции.

Интеграция будет выглядеть примерно так:

![Функция интеграции приложений](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Добавьте код, функция

Чтобы реализовать функцию, добавьте C# кода синтаксический анализ входящего HTTP-запроса и отправку сообщений электронной почты следующим образом:

1. Выберите **HttpTrigger1** работать в приложении-функции и замените C# код следующим кодом:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    Может появиться сообщение об ошибке только после сохранения нового кода.

1. Выберите **Сохранить** сохранить функцию.

### <a name="test-the-function-works"></a>Тестирование работы функции

Чтобы проверить функцию на портале, сначала выберите **журналы** в нижней части редактора кода. Затем выберите **теста** в правой части редактора кода. Используйте приведенный ниже код JSON, как **текст запроса**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Функция log сообщения появляются в **журналы** панели:

![Выходные данные журнала функции](media/howto-create-custom-rules/function-app-logs.png)

Через несколько минут **для** адрес электронной почты получает сообщение электронной почты со следующим содержимым:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Добавьте запрос Stream Analytics

Это решение использует запрос Stream Analytics, чтобы определить, когда устройство перестает отправлять данные телеметрии для более чем 120 секунд. Запрос использует данные телеметрии из концентратора событий в качестве входных данных. Задание отправляет результаты запроса к приложению-функции. В этом разделе вы настроите и задание Stream Analytics:

1. На портале Azure, перейдите к заданию Stream analytics в разделе **топологии задания** выберите **входные данные**, выберите **+ добавить потоковый вход**, а затем выберите **событий Центр**.
1. Используйте сведения в следующей таблице, чтобы настроить входные данные, используя созданный ранее концентратор событий, а затем выберите **Сохранить**:

    | Параметр | Value |
    | ------- | ----- |
    | Входной псевдоним | centraltelemetry |
    | Подписка | Ваша подписка |
    | пространство имен концентратора событий; | Пространство имен концентратора событий |
    | имя концентратора событий; | Использовать существующий - **centralexport** |

1. В разделе **топологии задания**выберите **выходные данные**, выберите **+ добавить**, а затем выберите **функции Azure**.
1. Используйте сведения в следующей таблице, чтобы настроить выходные данные, а затем выберите **Сохранить**:

    | Параметр | Value |
    | ------- | ----- |
    | Псевдоним выходных данных | emailnotification |
    | Подписка | Ваша подписка |
    | Приложение-функция | Ваше приложение-функция |
    | Функция  | HttpTrigger1 |

1. В разделе **топологии задания**выберите **запроса** и замените существующий запрос на следующий запрос SQL:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Щелкните **Сохранить**.
1. Чтобы запустить задание Stream Analytics, выберите **Обзор**, затем **запустить**, затем **теперь**, а затем **запустить**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Настроить экспорт в IoT Central

Перейдите к [приложения центра Интернета вещей](https://aka.ms/iotcentral) создан из шаблона Contoso. В этом разделе описано настройте приложение для потоковой передачи данных телеметрии в концентратор событий из его виртуальных устройств. Настройка экспорта:

1. Перейдите к **непрерывного экспорта данных** выберите **+ создать**, а затем **концентраторов событий**.
1. Используйте следующие параметры для настройки экспорта, а затем выберите **Сохранить**:

    | Параметр | Value |
    | ------- | ----- |
    | Название | Экспорт в Центры событий |
    | Enabled | Включен |
    | Пространство имен Центров событий | Имя пространства имен концентраторов событий |
    | концентратор событий; | centralexport |
    | Измерения | Включен |
    | Устройства | Выкл |
    | Шаблоны устройств | Выкл |

![Экспорт непрерывных данных конфигурации](media/howto-create-custom-rules/cde-configuration.png)

Подождите, пока состояние экспорта **под управлением** перед продолжением.

## <a name="test"></a>Проверка

Тестирование решения, вы можете отключить непрерывный Экспорт данных из центра Интернета вещей для имитации остановлена устройств:

1. В приложении IoT Central, перейдите к **непрерывного экспорта данных** и укажите **Экспорт в концентраторы событий** Экспорт конфигурации.
1. Задайте **включено** для **Off** и выберите **Сохранить**.
1. После по крайней мере две минуты **для** адрес электронной почты получает один или несколько сообщений электронной почты, которые выглядят как в следующем примере содержимого:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>Удаление ненужных элементов

Чтобы освободить после этого практического руководства и избежать ненужных затрат, удалите **DetectStoppedDevices** группу ресурсов на портале Azure.

Для удаления приложения IoT Central из **управления** страницы в приложении.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали следующее:

* Данные телеметрии из приложения IoT Central с помощью Stream *непрерывный Экспорт данных*.
* Создайте запрос Stream Analytics, которое обнаруживает, когда устройство была остановлена, отправка данных.
* Отправьте уведомление по электронной почте, с помощью функций Azure и служб SendGrid.

Теперь, когда вы знаете, как создать настраиваемые правила и уведомления, мы предлагаем — Узнайте, как [визуализировать и анализировать данные центра Интернета вещей Azure на панели мониторинга Power BI](howto-connect-powerbi.md).
