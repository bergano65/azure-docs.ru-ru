---
title: Расширение IoT Central Azure с помощью настраиваемых правил и уведомлений | Документация Майкрософт
description: В качестве разработчика решения настройте IoT Centralное приложение для отправки уведомлений по электронной почте при прекращении отправки телеметрических данных устройством. Это решение использует Azure Stream Analytics, функции Azure и SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: bdaa08e8c3b104c7269c1fb4169779d98b4e0880
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895725"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid-preview-features"></a>Расширьте IoT Central Azure с помощью настраиваемых правил, используя Stream Analytics, функции Azure и SendGrid (Предварительная версия функций).

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

В этом пошаговом руководство показано, как разработчик решения, как расширить приложение IoT Central с помощью настраиваемых правил и уведомлений. В примере показано, как отправить уведомление оператору, когда устройство прекращает отправку данных телеметрии. Решение использует [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) запрос, чтобы определить, когда устройство остановило отправку данных телеметрии. Задание Stream Analytics использует [функции Azure](https://docs.microsoft.com/azure/azure-functions/) для отправки уведомлений по электронной почте с помощью [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

В этом пошаговом руководство показано, как расширить IoT Central за пределами того, что он уже может сделать с помощью встроенных правил и действий.

В этом пошаговом руководство вы узнаете, как выполнять следующие задачи:

* Потоковая передача телеметрии из IoT Central приложения с помощью *непрерывного экспорта данных*.
* Создание Stream Analytics запроса, который определяет, когда устройство остановило отправку данных.
* Отправка уведомления по электронной почте с помощью функций Azure и SendGrid Services.

## <a name="prerequisites"></a>Технические условия

Чтобы выполнить действия, описанные в этом руководстве, вам потребуется активная подписка Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

### <a name="iot-central-application"></a>Приложение IoT Central

Создайте приложение IoT Central на веб-сайте [диспетчера приложений Azure IOT Central](https://aka.ms/iotcentral) со следующими параметрами:

| Параметр | Value |
| ------- | ----- |
| План оплаты | Оплата по мере использования |
| Шаблон приложения | Аналитика в магазине — мониторинг условий |
| имя приложения; | Примите значение по умолчанию или выберите собственное имя. |
| URL-адрес | Примите значение по умолчанию или выберите собственный уникальный префикс URL-адреса. |
| Каталог | Клиент Azure Active Directory |
| Подписка на Azure | Ваша подписка Azure. |
| Регион | Ближайший регион |

В примерах и снимках экрана в этой статье используется **США** регион. Выберите расположение, близкое к вам, и убедитесь, что вы создали все ресурсы в том же регионе.

Этот шаблон приложения включает два смоделированных устройства термостата, которые отправляют данные телеметрии.

### <a name="resource-group"></a>группа ресурсов.

Используйте [портал Azure, чтобы создать группу ресурсов](https://portal.azure.com/#create/Microsoft.ResourceGroup) с именем **детектстоппеддевицес** , которая будет содержать другие создаваемые ресурсы. Создайте ресурсы Azure в том же расположении, что и приложение IoT Central.

### <a name="event-hubs-namespace"></a>пространство имен Центров событий;

Используйте [портал Azure, чтобы создать пространство имен концентраторов событий](https://portal.azure.com/#create/Microsoft.EventHub) со следующими параметрами:

| Параметр | Value |
| ------- | ----- |
| Name    | Выберите имя пространства имен |
| Уровень цен | "Базовый" |
| Subscription | Ваша подписка |
| группа ресурсов. | детектстоппеддевицес |
| Location | Восточная часть США |
| Единицы пропускной способности | 1 |

### <a name="stream-analytics-job"></a>Задание Stream Analytics

Используйте [портал Azure, чтобы создать задание Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) со следующими параметрами:

| Параметр | Value |
| ------- | ----- |
| Name    | Выберите имя задания |
| Subscription | Ваша подписка |
| группа ресурсов. | детектстоппеддевицес |
| Location | Восточная часть США |
| Среда размещения | В облаке |
| Единицы потоковой передачи | 3 |

### <a name="function-app"></a>Приложение-функция

Используйте [портал Azure, чтобы создать приложение-функцию](https://portal.azure.com/#create/Microsoft.FunctionApp) со следующими параметрами:

| Параметр | Value |
| ------- | ----- |
| Имя приложения.    | Выберите имя приложения функции |
| Subscription | Ваша подписка |
| группа ресурсов. | детектстоппеддевицес |
| ОС | Windows |
| План размещения | План потребления |
| Location | Восточная часть США |
| Стек времени выполнения | .NET |
| Storage | Создание |

### <a name="sendgrid-account"></a>Учетная запись SendGrid

Используйте [портал Azure, чтобы создать учетную запись SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) со следующими параметрами:

| Параметр | Value |
| ------- | ----- |
| Name    | Выберите имя учетной записи SendGrid |
| Пароль | Создание пароля |
| Subscription | Ваша подписка |
| группа ресурсов. | детектстоппеддевицес |
| Уровень цен | Бесплатный F1 |
| Контактные данные | Введите необходимую информацию |

После создания всех необходимых ресурсов Группа ресурсов **детектстоппеддевицес** выглядит как на следующем снимке экрана:

![Обнаружение группы ресурсов "остановленные устройства"](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Создание концентратора событий

Вы можете настроить IoT Central приложение для непрерывного экспорта данных телеметрии в концентратор событий. В этом разделе вы создадите концентратор событий для получения данных телеметрии из приложения IoT Central. Концентратор событий доставляет данные телеметрии в задание Stream Analytics для обработки.

1. В портал Azure перейдите к пространству имен концентраторов событий и выберите **+ концентратор событий**.
1. Назовите концентратор событий **централекспорт**и выберите **создать**.

Пространство имен концентраторов событий выглядит как на следующем снимке экрана:

![пространство имен Центров событий;](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Получить ключ API SendGrid

Приложению-функции требуется ключ API SendGrid для отправки сообщений электронной почты. Чтобы создать ключ API SendGrid, выполните следующие действия.

1. В портал Azure перейдите к своей учетной записи SendGrid. Затем выберите **Управление** , чтобы получить доступ к учетной записи SendGrid.
1. В учетной записи SendGrid выберите **Параметры**, а затем **ключи API**. Выберите команду " **создать ключ API**":

    ![Создание ключа API SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. На странице **Создание ключа API** создайте ключ с именем **азурефунктионакцесс** и разрешениями на **полный доступ** .
1. Запишите ключ API, он понадобится при настройке приложения-функции.

## <a name="define-the-function"></a>Определение функции

Это решение использует приложение "функции Azure" для отправки уведомления по электронной почте, когда задание Stream Analytics обнаруживает остановленное устройство. Чтобы создать приложение функции, сделайте следующее:

1. В портал Azure перейдите к экземпляру **службы приложений** в группе ресурсов **детектстоппеддевицес** .
1. Выберите **+** , чтобы создать новую функцию.
1. На странице **Выбор среды разработки** выберите **на портале** и нажмите кнопку **продолжить**.
1. На странице **Создание функции** выберите **веб-перехватчик + API** , а затем щелкните **создать**.

На портале создается функция по умолчанию с именем **HttpTrigger1**:

![Функция триггера HTTP по умолчанию](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Настройка привязок функций

Чтобы отправить сообщения электронной почты с помощью SendGrid, необходимо настроить привязки для функции следующим образом:

1. Выберите **интегрировать**, выберите выходной **http ($Return)** , а затем нажмите кнопку **Удалить**.
1. Выберите **+ новый выход**, затем выберите **SendGrid**и нажмите **кнопку Выбрать**. Нажмите кнопку **установить** , чтобы установить расширение SendGrid.
1. После завершения установки выберите **использовать возвращаемое значение функции**. Добавьте допустимый **адрес для** получения уведомлений по электронной почте.  Добавьте допустимый **адрес из адреса** , который будет использоваться в качестве отправителя электронной почты.
1. Выберите **создать** рядом с **параметром приложения ключа API SendGrid**. Введите **сендгридапикэй** в качестве ключа и ключ API SendGrid, который вы заговорили ранее как значение. Щелкните **Создать**.
1. Нажмите кнопку **сохранить** , чтобы сохранить привязки SendGrid для функции.

Параметры интеграции выглядят как на следующем снимке экрана:

![Интеграция с приложением функции](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Добавление кода функции

Чтобы реализовать функцию, добавьте C# код для синтаксического анализа входящего HTTP-запроса и отправки сообщений электронной почты следующим образом:

1. Выберите функцию **HttpTrigger1** в приложении функции и замените C# код следующим кодом:

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

    Вы можете увидеть сообщение об ошибке, пока не будет сохранен новый код.

1. Нажмите кнопку **сохранить** , чтобы сохранить функцию.

### <a name="test-the-function-works"></a>Проверка работы функции

Чтобы проверить функцию на портале, сначала выберите **журналы** в нижней части редактора кода. Затем выберите **тест** справа от редактора кода. Используйте следующий код JSON в качестве **текста запроса**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Сообщения журнала функций отображаются на панели **журналы** :

![Выходные данные журнала функций](media/howto-create-custom-rules/function-app-logs.png)

Через несколько минут адрес электронной почты будет получать сообщение **электронной почты со** следующим содержимым:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Добавление Stream Analytics запроса

Это решение использует Stream Analytics запрос для обнаружения того, когда устройство прекращает отправку данных телеметрии более 120 секунд. В качестве входных данных в запросе используются данные телеметрии из концентратора событий. Задание отправляет результаты запроса в приложение функции. В этом разделе вы настроите задание Stream Analytics:

1. В портал Azure перейдите к заданию Stream Analytics в разделе **топология заданий** выберите **входные данные**, щелкните **+ добавить потоковый вход**и выберите **концентратор событий**.
1. Используйте сведения в следующей таблице для настройки входных данных с помощью созданного ранее концентратора событий, а затем выберите **сохранить**:

    | Параметр | Value |
    | ------- | ----- |
    | Псевдоним входных данных | централтелеметри |
    | Subscription | Ваша подписка |
    | пространство имен концентратора событий; | Пространство имен концентратора событий |
    | имя концентратора событий; | Использовать existing- **централекспорт** |

1. В разделе **топология заданий**выберите **выходные данные**, нажмите кнопку **+ Добавить**, а затем выберите **функция Azure**.
1. Используйте сведения в следующей таблице, чтобы настроить выходные данные, а затем выберите **сохранить**:

    | Параметр | Value |
    | ------- | ----- |
    | Псевдоним выходных данных | емаилнотификатион |
    | Subscription | Ваша подписка |
    | Приложение-функция | Приложение функции |
    | Функция  | HttpTrigger1 |

1. В разделе **топология заданий**выберите **запрос** и замените существующий запрос на следующий SQL:

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
1. Чтобы запустить задание Stream Analytics, выберите **Обзор**, затем **запустите**, **а затем** **запустите**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Настройка экспорта в IoT Central

На веб-сайте [Azure IOT Central Application Manager](https://aka.ms/iotcentral) перейдите к IOT Centralному приложению, созданному на основе шаблона Contoso. В этом разделе вы настроите приложение для потоковой передачи данных телеметрии из виртуальных устройств в концентратор событий. Чтобы настроить экспорт, выполните следующие действия.

1. Перейдите на страницу **Экспорт данных** , выберите **+ создать**, а затем **концентраторы событий Azure**.
1. Используйте следующие параметры для настройки экспорта, а затем выберите **сохранить**:

    | Параметр | Value |
    | ------- | ----- |
    | Отображаемое имя | Экспорт в концентраторы событий |
    | Включено | Включено |
    | пространство имен Центров событий; | Имя пространства имен концентраторов событий |
    | концентратор событий; | централекспорт |
    | Измерения | Включено |
    | Устройства | Выключено |
    | Шаблоны устройств | Выключено |

![Настройка непрерывного экспорта данных](media/howto-create-custom-rules/cde-configuration.png)

Прежде чем продолжить, подождите, пока состояние экспорта будет **выполняться** .

## <a name="test"></a>Тестирование

Чтобы протестировать решение, можно отключить экспорт непрерывных данных из IoT Central в имитацию остановленных устройств:

1. В приложении IoT Central перейдите на страницу **Export Data (экспорт данных** ) и выберите **Экспорт в конфигурацию экспорта концентраторов событий** .
1. Установите для параметра **включено** значение **выкл** и нажмите кнопку **сохранить**.
1. По истечении двух минут адрес **электронной почты будет** принимать одно или несколько сообщений, которые выглядят как в следующем примере:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Удаление ненужных элементов

Чтобы устранить эту работу и избежать ненужных затрат, удалите группу ресурсов **детектстоппеддевицес** в портал Azure.

Вы можете удалить IoT Centralное приложение со страницы **Управление** в приложении.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали следующее:

* Потоковая передача телеметрии из IoT Central приложения с помощью *непрерывного экспорта данных*.
* Создание Stream Analytics запроса, который определяет, когда устройство остановило отправку данных.
* Отправка уведомления по электронной почте с помощью функций Azure и SendGrid Services.

Теперь, когда вы узнали, как создавать настраиваемые правила и уведомления, предлагаем следующий шаг: Узнайте, как [расширить Azure IOT Central с помощью пользовательской аналитики](howto-create-custom-analytics.md).
