---
title: Триггеры таймера для службы "Функции Azure"
description: Узнайте, как использовать триггеры с таймерами в функциях Azure.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: ebe7916900ded20dc6555c9c28277dea889539a3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230968"
---
# <a name="timer-trigger-for-azure-functions"></a>Триггеры таймера для службы "Функции Azure" 

В этой статье описывается, как работать с триггерами таймера в службе "Функции Azure". Триггер таймера позволяет выполнять функцию по расписанию. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Триггер таймера доступен в пакете NuGet [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) версии 2.х. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

Триггер таймера доступен в пакете NuGet [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) версии 3.х. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Пример

Языковой пример см. в разделах:

* [C#](#c-example)
* [Сценарий C# (CSX)](#c-script-example)
* [F#](#f-example)
* [Java](#java-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>Пример C#

The following example shows a [C# function](functions-dotnet-class-library.md) that is executed each time the minutes have a value divisible by five (eg if the function starts at 18:57:00, the next performance will be at 19:00:00). The [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object is passed into the function.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Пример сценария C#

В следующем примере показаны привязка триггера таймера в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Эта функция выполняет запись в журнал, указывая, когда ее вызов выполняется из-за пропущенного запуска по расписанию. The [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object is passed into the function.

Данные привязки в файле *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Ниже приведен код скрипта C#.

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>Пример F#

В следующем примере показаны привязка триггера таймера в файле *function.json* и [функция сценария F#](functions-reference-fsharp.md), которая использует эту привязку. Эта функция выполняет запись в журнал, указывая, когда ее вызов выполняется из-за пропущенного запуска по расписанию. The [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object is passed into the function.

Данные привязки в файле *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Ниже приведен код сценария F#.

```fsharp
let Run(myTimer: TimerInfo, log: ILogger ) =
    if (myTimer.IsPastDue) then
        log.LogInformation("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.LogInformation(sprintf "F# function executed at %s!" now)
```

### <a name="java-example"></a>Пример Java

Следующий пример функции активируется и выполняется каждые пять минут. Аннотация `@TimerTrigger` для функции определяет расписание, используя тот же формат строки, что и [выражения CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

### <a name="javascript-example"></a>Пример JavaScript

В следующем примере показаны привязка триггера таймера в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Эта функция выполняет запись в журнал, указывая, когда ее вызов выполняется из-за пропущенного запуска по расписанию. A [timer object](#usage) is passed into the function.

Данные привязки в файле *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Ниже показан код JavaScript.

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

### <a name="python-example"></a>Пример на Python

The following example uses a timer trigger binding whose configuration is described in the *function.json* file. The actual [Python function](functions-reference-python.md) that uses the binding is described in the *__init__.py* file. The object passed into the function is of type [azure.functions.TimerRequest object](/python/api/azure-functions/azure.functions.timerrequest). The function logic writes to the logs indicating whether the current invocation is due to a missed schedule occurrence. 

Данные привязки в файле *function.json*:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Ниже приведен код Python.

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

## <a name="attributes"></a>Атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Конструктор атрибута принимает выражение CRON или `TimeSpan`. `TimeSpan` можно использовать, только если приложение-функция выполняется в рамках плана службы приложений. В приведенном ниже примере показано выражение CRON.

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

## <a name="configuration"></a>Настройка

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `TimerTrigger`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Н/Д | Этому свойству необходимо присвоить значение "timerTrigger". Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Н/Д | Для этого свойства необходимо задать значение "in". Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Н/Д | Имя переменной, представляющей объект таймера в коде функции. | 
|**schedule**|**ScheduleExpression**|Значение [выражения CRON](#ncrontab-expressions) или [TimeSpan](#timespan). `TimeSpan` можно использовать только для приложения-функции, которая работает в плане службы приложений. Вы можете поместить выражение расписания в параметр приложения и присвоить этому свойству имя параметра приложения, заключенное в знаки **%** , например: "%ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Если настроено значение `true`, функция вызывается при запуске среды выполнения. Например, среда выполнения запускается, когда приложение-функция выходит из спящего режима (в который она перешла из-за отсутствия активности), when the function app restarts due to function changes, and when the function app scales out. So **runOnStartup** should rarely if ever be set to `true`, especially in production. |
|**useMonitor**|**UseMonitor**|Установите значение `true` или`false`, чтобы указать, следует ли отслеживать расписание. При мониторинге расписания его экземпляры сохраняются, чтобы обеспечить его корректную обработку даже при перезапуске экземпляров приложения-функции. If not set explicitly, the default is `true` for schedules that have a recurrence interval greater than or equal to 1 minute. Для расписаний, выполняющихся чаще одного раза в минуту, значением по умолчанию является `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Мы не рекомендуем задавать параметр **runOnStartup** для `true` в рабочей среде. Использование этого параметра заставляет код выполняться в очень непредсказуемое время. В определенных рабочих ситуациях эти дополнительные выполнения могут привести значительно более высоким затратам для приложений, размещенных в планах потребления. For example, with **runOnStartup** enabled the trigger is invoked whenever your function app is scaled. Убедитесь, что вы полностью понимаете рабочее поведение своих функций перед включением **runOnStartup** в рабочей среде.   

## <a name="usage"></a>Использование

When a timer trigger function is invoked, a timer object is passed into the function. Далее представлен JSON в качестве примера объекта таймера.

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

Значение свойства `IsPastDue` — `true`, когда текущая функция вызывается позже запланированного. Например перезапуск приложения-функции может привести к тому, что вызов будет пропущен.

## <a name="ncrontab-expressions"></a>NCRONTAB expressions 

Azure Functions uses the [NCronTab](https://github.com/atifaziz/NCrontab) library to interpret NCRONTAB expressions. An NCRONTAB exppression is similar to a CRON expression except that it includes an additional sixth field at the beginning to use for time precision in seconds:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Каждое поле может принимать значение одного из следующих типов:

|Тип  |Пример  |Когда активируется  |
|---------|---------|---------|
|Определенное значение |<nobr>"0 5 * * * *"</nobr>|в hh:05:00, где hh — каждый час (один раз в час)|
|Все значения (`*`)|<nobr>"0 * 5 * * *"</nobr>|в 5:mm:00 каждый день, где mm — каждая минута часа (60 раз в день)|
|Диапазон (оператор `-`)|<nobr>"5-7 * * * * *"</nobr>|в hh:mm:05, hh:mm:06 и hh:mm:07, где hh:mm — каждая минута каждого часа (3 раза в минуту)|
|Набор значений (оператор `,`)|<nobr>"5,8,10 * * * * *"</nobr>|в hh:mm:05, hh:mm:08 и hh:mm:10, где hh:mm — каждая минута каждого часа (3 раза в минуту)|
|Значение интервала (оператор `/`)|<nobr>"0 */5 * * * *"</nobr>|в hh:05:00, hh:10:00, hh:15:00 и так далее до hh:55:00, где hh — каждый час (12 раз в час)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB examples

Here are some examples of NCRONTAB expressions you can use for the timer trigger in Azure Functions.

|Пример|Когда активируется  |
|---------|---------|
|`"0 */5 * * * *"`|через каждые пять минут|
|`"0 0 * * * *"`|через каждый час|
|`"0 0 */2 * * *"`|через каждые 2 часа|
|`"0 0 9-17 * * *"`|один раз в час с 9:00 до 17:00|
|`"0 30 9 * * *"`|каждый день в 9:30|
|`"0 30 9 * * 1-5"`|каждый рабочий день в 9:30|
|`"0 30 9 * Jan Mon"`|в 9:30 каждый понедельник в январе|


### <a name="ncrontab-time-zones"></a>NCRONTAB time zones

Числа в выражении CRON представляют собой время и дату, а не временной интервал. Например, значение 5 в поле `hour` означает 5:00, а не каждые 5 часов.

Часовой пояс по умолчанию, используемый с выражениями CRON — время в формате UTC. Если нужно использовать другой часовой пояс в выражении CRON, создайте параметр приложения с именем `WEBSITE_TIME_ZONE` для приложения-функции. В качестве значения задайте имя нужного часового пояса, как показано в статье [Microsoft Time Zone Index](https://technet.microsoft.com/library/cc749073) (Индексы часовых поясов Майкрософт). 

Например, *восточное поясное время* — UTC-05:00. To have your timer trigger fire at 10:00 AM EST every day, use the following NCRONTAB expression that accounts for UTC time zone:

```
"0 0 15 * * *"
``` 

Или создайте параметр приложения с именем `WEBSITE_TIME_ZONE` для приложения-функции и задайте **Восточное время США** в качестве значения.  Then uses the following NCRONTAB expression: 

```
"0 0 10 * * *"
``` 

Если используется `WEBSITE_TIME_ZONE`, настраивается изменение времени в соответствии с определенным часовым поясом, например с переходом на летнее время. 

## <a name="timespan"></a>Интервал времени

 `TimeSpan` можно использовать только для приложения-функции, которая работает в плане службы приложений.

В отличие от выражения CRON значение `TimeSpan` задает интервал времени между каждым вызовом функции. Если функция будет выполняться дольше заданного временного интервала, она немедленно будет активирована таймером.

Когда значения выражения `hh` в виде стоки меньше 24, `TimeSpan` будет использовать формат `hh:mm:ss`. Когда первые две цифры больше или равны 24, будет использован следующий формат `dd:hh:mm`. Ниже приведены некоторые примеры:

|Пример |Когда активируется  |
|---------|---------|
|"01:00:00" | каждый час        |
|"00:01:00"|каждую минуту         |
|"24:00:00" | every 24 hours        |
|"1.00:00:00" | every day        |

## <a name="scale-out"></a>Расширение

Если приложение-функция будет развернуто на несколько экземпляров, то только один из экземпляров активируемой по таймеру функции выполняется для всех экземпляров.

## <a name="function-apps-sharing-storage"></a>Совместное использование хранилища приложениями-функциями

If you are sharing storage accounts across function apps that are not deployed to app service, you might need to explicitly assign host ID to each app.

| Версия службы "Функции" | Параметр                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x               | `AzureFunctionsWebHost__hostid` environment variable |
| 1.x               | `id` in *host.json*                                  |

You can omit the identifying value or manually set each function app's identifying configuration to a different value.

The timer trigger uses a storage lock to ensure that there is only one timer instance when a function app scales out to multiple instances. If two function apps share the same identifying configuration and each uses a timer trigger, only one timer runs.

## <a name="retry-behavior"></a>Поведение при повторе

В отличие от триггера очереди триггер таймера не осуществляет повторную попытку после того, как произошла ошибка выполнения функции. Когда функция возвращает ошибку, следующий раз она будет вызвана только по расписанию.

## <a name="troubleshooting"></a>Устранение неисправностей

Дополнительные сведения о том, что делать, когда триггер таймера неисправен, см. в разделе [Расследование проблем, когда функции, вызываемые таймером, не срабатывают](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Перейдите к краткому руководству по использованию триггера таймера](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
