---
title: Расширьте Azure IoT Central с помощью пользовательских правил и уведомлений Документы Майкрософт
description: В качестве разработчика решений направьте приложение IoT Central для отправки уведомлений по электронной почте, когда устройство перестает отправлять телеметрию. Это решение использует аналитику потоков Azure, функции Azure и SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e161cf83662df671b8cfb100ddc12c3b3e7359f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158152"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Расширьте Центр Интернета Сиот с помощью пользовательских правил с использованием Stream Analytics, функций Azure и SendGrid



В этом руководстве, как разработчике решений, показано, как расширить приложение IoT Central с помощью пользовательских правил и уведомлений. Пример показывает отправку уведомления оператору, когда устройство перестает отправлять телеметрию. Решение использует запрос [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) для обнаружения, когда устройство перестало отправлять телеметрию. Задание Stream Analytics использует [функции Azure](https://docs.microsoft.com/azure/azure-functions/) для отправки сообщений с уведомлением с помощью [SendGrid.](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)

Это руководство показывает, как расширить IoT Central за пределы того, что он уже может сделать со встроенными правилами и действиями.

В этом руководстве вы узнаете, как:

* Телеметрия потока из приложения IoT Central с использованием *непрерывного экспорта данных.*
* Создайте запрос Stream Analytics, который обнаруживает, когда устройство перестало отправлять данные.
* Отправить уведомление по электронной почте с помощью служб Azure Functions и SendGrid.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этом руководстве, вам потребуется активная подписка Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

### <a name="iot-central-application"></a>Приложение IoT Central

Создание центрального приложения IoT на веб-сайте [менеджера приложений Azure IoT](https://aka.ms/iotcentral) со следующими настройками:

| Параметр | Значение |
| ------- | ----- |
| Ценовой план | Standard |
| Шаблон приложения | Аналитика в магазине - мониторинг состояния |
| имя приложения; | Примите значение по умолчанию или выберите собственное имя |
| URL-адрес | Примите значение по умолчанию или выберите свой собственный уникальный префикс URL |
| Каталог | Ваш арендатор Active Directory Azure |
| Подписка Azure. | Ваша подписка Azure. |
| Регион | Ближайший регион |

Примеры и скриншоты в этой статье используют регион **Соединенных Штатов.** Выберите место рядом с вами и убедитесь, что вы создаете все свои ресурсы в одном регионе.

Этот шаблон приложения включает в себя два смоделированных термостата устройств, которые отправляют телеметрии.

### <a name="resource-group"></a>Группа ресурсов

Используйте [портал Azure для создания группы ресурсов](https://portal.azure.com/#create/Microsoft.ResourceGroup) под названием **DetectStoppedDevices** для хранения других создаваемых ресурсов. Создавайте ресурсы Azure в том же месте, что и приложение IoT Central.

### <a name="event-hubs-namespace"></a>пространство имен Центров событий;

Используйте [портал Azure для создания пространства имен событий концентраторов](https://portal.azure.com/#create/Microsoft.EventHub) со следующими настройками:

| Параметр | Значение |
| ------- | ----- |
| name    | Выберите имя в пространстве имен |
| Ценовая категория | Basic |
| Подписка | Ваша подписка |
| Группа ресурсов | ОбнаружитьОстановленныеустройства |
| Расположение | Восточная часть США |
| Единицы пропускной способности | 1 |

### <a name="stream-analytics-job"></a>Задание Stream Analytics

Используйте [портал Azure для создания задания Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) со следующими настройками:

| Параметр | Значение |
| ------- | ----- |
| name    | Выберите имя работы |
| Подписка | Ваша подписка |
| Группа ресурсов | ОбнаружитьОстановленныеустройства |
| Расположение | Восточная часть США |
| Среда размещения | Cloud |
| Единицы потоковой передачи | 3 |

### <a name="function-app"></a>Приложение-функция

Используйте [портал Azure для создания функционального приложения](https://portal.azure.com/#create/Microsoft.FunctionApp) со следующими настройками:

| Параметр | Значение |
| ------- | ----- |
| Имя приложения.    | Выберите имя приложения функции |
| Подписка | Ваша подписка |
| Группа ресурсов | ОбнаружитьОстановленныеустройства |
| OS | Windows |
| План размещения | План потребления |
| Расположение | Восточная часть США |
| Стек времени выполнения | .NET |
| Хранилище | Создание |

### <a name="sendgrid-account"></a>Учетная запись SendGrid

Используйте [портал Azure для создания учетной записи SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) со следующими настройками:

| Параметр | Значение |
| ------- | ----- |
| name    | Выберите имя учетной записи SendGrid |
| Пароль | Создание пароля |
| Подписка | Ваша подписка |
| Группа ресурсов | ОбнаружитьОстановленныеустройства |
| Ценовая категория | Бесплатный F1 |
| Контактные данные | Заполните необходимую информацию |

Когда вы создали все необходимые ресурсы, ваша группа ресурсов **DetectStoppedDevices** выглядит следующим скриншотом:

![Обнаружение группы ресурсов остановленных устройств](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Создание концентратора событий

Вы можете настроить приложение IoT Central для непрерывной экспорта телеметрии в концентратор событий. В этом разделе создается концентратор событий для получения телеметрии из приложения IoT Central. Концентратор событий обеспечивает телеметрию для обработки stream Analytics.

1. На портале Azure перейдите в пространство имен концентров событий и выберите **концентратор событий.**
1. Назовите свой центр событий **centralexport**и выберите **Создать**.

Пространство имен Event Hubs выглядит следующим скриншотом:

![пространство имен Центров событий;](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Получите ключ API SendGrid

Приложению функции нужен ключ API SendGrid для отправки сообщений электронной почты. Для создания ключа API SendGrid:

1. На портале Azure перейдите к учетной записи SendGrid. Затем выберите **Управление** для доступа к учетной записи SendGrid.
1. В учетной записи SendGrid выберите **Настройки,** а затем **API Keys.** Выберите **Создать API ключ:**

    ![Создание ключа API SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. На странице **«Создайте ключ»** создайте ключ под названием **AzureFunctionAccess** с разрешениями **с полным доступом.**
1. Сделайте примечание ключа API, вам это нужно при настройке приложения функции.

## <a name="define-the-function"></a>Определить функцию

Это решение использует приложение Azure Functions для отправки уведомления по электронной почте, когда задание Stream Analytics обнаруживает остановленное устройство. Для создания приложения функции:

1. На портале Azure перейдите в экземпляр **службы приложений** в группе ресурсов **DetectStoppedDevices.**
1. Выберите **+** для создания новой функции.
1. На странице **CHOOSE A DEVELOPMENT ENVIRONMENT** выберите **In-portal,** а затем выберите **Продолжить**.
1. На странице **CREATE A FUNCTION** выберите **Webhook и API,** а затем выберите **Create**.

Портал создает функцию по умолчанию под названием **HttpTrigger1:**

![Функция триггера HTTP по умолчанию](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Настройка привязок функций

Чтобы отправить электронные письма с Помощью SendGrid, необходимо настроить привязки для вашей функции следующим образом:

1. Выберите **Integrate,** выберите вывод **HTTP ($return)** и затем выберите **удаление.**
1. Выберите **новый выход,** затем выберите **SendGrid,** а затем **выберите .** Выберите **установку** для установки расширения SendGrid.
1. Когда установка завершится, выберите **значение возврата функции использования.** Добавьте действительный **адрес** для получения уведомлений по электронной почте.  Добавьте действительный **от адреса** для использования в качестве отправителя электронной почты.
1. Выберите **новый** рядом с **настройкой ключевого приложения SendGrid API.** Введите **SendGridAPIKey** в качестве ключа и клавишу API SendGrid, который вы отмечали ранее в качестве значения. Затем выберите **Создать**.
1. Выберите **Сохранить,** чтобы сохранить привязки SendGrid для вашей функции.

Настройки интеграции выглядят следующим скриншотом:

![Интеграция функциональных приложений](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Добавление кода функции

Чтобы реализовать свою функцию, добавьте код C,, чтобы разобрать входящий запрос HTTP и отправить электронные письма следующим образом:

1. Выберите функцию **HttpTrigger1** в приложении функции и замените код C-кода следующим кодом:

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

    Вы можете видеть сообщение об ошибке до тех пор, пока не сохраните новый код.

1. Выберите **Сохранить,** чтобы сохранить функцию.

### <a name="test-the-function-works"></a>Проверка работы функции

Чтобы протестировать функцию на портале, сначала выберите **журналы** в нижней части редактора кода. Затем выберите **Тест** справа от редактора кода. Используйте следующий JSON в качестве **органа запроса:**

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Сообщения журнала функций отображаются в панели **журналов:**

![Выход журнала функции](media/howto-create-custom-rules/function-app-logs.png)

Через несколько минут адрес электронной почты **To** получает электронное письмо со следующим содержанием:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Добавление запроса Stream Analytics

Это решение использует запрос Stream Analytics для обнаружения, когда устройство перестает отправлять телеметрию более 120 секунд. В качестве ввода запрос использует телеметрию из концентратора событий. Задание отправляет результаты запроса в приложение функции. В этом разделе вы настраиваете задание Stream Analytics:

1. На портале Azure перейдите к заданию Stream Analytics, под **топологией заданий** выберите **входные данные,** выберите **ввод потока,** а затем выберите **концентратор событий.**
1. Используйте информацию в следующей таблице, чтобы настроить входные данные с помощью созданного ранее концентратора событий, а затем выберите **Сохранить:**

    | Параметр | Значение |
    | ------- | ----- |
    | Псевдоним входных данных | центральнаятелеметрия |
    | Подписка | Ваша подписка |
    | пространство имен концентратора событий; | Пространство имен event Hub |
    | имя концентратора событий; | Использование существующих - **центральныйэкспорт** |

1. В **топологии вакансий**выберите **выходы,** выберите **и переместите** **функцию Azure.**
1. Используйте информацию в следующей таблице, чтобы настроить выход, а затем выберите **Сохранить:**

    | Параметр | Значение |
    | ------- | ----- |
    | Псевдоним выходных данных | электронное уведомление |
    | Подписка | Ваша подписка |
    | Приложение-функция | Приложение функции |
    | Компонент  | HttpTrigger1 |

1. В **топологии вакансий**выберите **запрос** и замените существующий запрос следующим s'L:

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

1. Нажмите кнопку **Сохранить**.
1. Чтобы начать работу Stream Analytics, выберите **Обзор,** затем **запустите,** затем **сейчас,** а затем **запустите:**

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Настройка экспорта в IoT Central

На веб-сайте [менеджера приложений Azure IoT Central](https://aka.ms/iotcentral) перейдите к приложению IoT Central, созданному из шаблона Contoso. В этом разделе вы настраиваете приложение для потоковой передачи телеметрии с смоделированных устройств в концентратор событий. Для настройки экспорта:

1. Перейдите на страницу **экспорта данных,** выберите **новые,** а затем **концентраторы событий Azure.**
1. Используйте следующие настройки для настройки экспорта, а затем выберите **Сохранить:**

    | Параметр | Значение |
    | ------- | ----- |
    | Отображаемое имя | Экспорт в центры событий |
    | Активировано | С |
    | пространство имен Центров событий; | Имя пространства имен событий Концентраторы |
    | концентратор событий; | центральныйэкспорт |
    | Измерения | С |
    | Устройства | Выкл. |
    | Шаблоны устройств | Выкл. |

![Настройка непрерывного экспорта данных](media/howto-create-custom-rules/cde-configuration.png)

Подождите, пока статус экспорта **будет запущен,** прежде чем продолжить работу.

## <a name="test"></a>Тест

Чтобы протестировать решение, можно отключить непрерывный экспорт данных из IoT Central на моделируемые остановленные устройства:

1. В приложении IoT Central перейдите на страницу **экспорта данных** и выберите конфигурацию **экспорта экспорта экспорта экспорта экспорта экспорта экспорта «Экспорт к центрам событий».**
1. Набор **включен,** чтобы **выключить** и выбрать **Сохранить**.
1. По крайней мере, через две минуты адрес электронной почты **To** получает одно или несколько писем, похожих на следующий пример содержимого:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Удаление ненужных элементов

Чтобы увести порядок после этого способ и избежать ненужных расходов, удалите группу ресурсов **DetectStoppedDevices** на портале Azure.

Вы можете удалить центральное приложение IoT со страницы **управления** в приложении.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали следующее:

* Телеметрия потока из приложения IoT Central с использованием *непрерывного экспорта данных.*
* Создайте запрос Stream Analytics, который обнаруживает, когда устройство перестало отправлять данные.
* Отправить уведомление по электронной почте с помощью служб Azure Functions и SendGrid.

Теперь, когда вы знаете, как создавать пользовательские правила и уведомления, следующий шаг должен узнать, как [расширить Azure IoT Central с помощью пользовательской аналитики.](howto-create-custom-analytics.md)
