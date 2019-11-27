---
title: Триггеры таймера для службы "Функции Azure"
description: Узнайте, как использовать триггеры с таймерами в функциях Azure.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 143a05944799ff04f9c21384f85a4b00cc65b750
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74545730"
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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

В следующем примере показана [ C# функция](functions-dotnet-class-library.md) , которая выполняется каждый раз, когда в минутах имеется значение, кратное пяти (например, если функция начинается в 18:57:00, следующая производительность будет составлять 19:00:00). Объект [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) передается в функцию.

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

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

В следующем примере показаны привязка триггера таймера в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Эта функция выполняет запись в журнал, указывая, когда ее вызов выполняется из-за пропущенного запуска по расписанию. Объект [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) передается в функцию.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

В следующем примере показаны привязка триггера таймера в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Эта функция выполняет запись в журнал, указывая, когда ее вызов выполняется из-за пропущенного запуска по расписанию. [Объект Timer](#usage) передается в функцию.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

В следующем примере используется привязка триггера таймера, конфигурация которой описана в файле *Function. JSON* . Фактическая [функция Python](functions-reference-python.md) , использующая привязку, описана в файле  *__init__. корректировки* . Объект, переданный в функцию, имеет тип [Azure. functions. тимеррекуест](/python/api/azure-functions/azure.functions.timerrequest). Логика функции записывает в журналы, указывающие, вызван ли текущий вызов из-за пропущенного события расписания. 

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

## <a name="attributes-and-annotations"></a>Атрибуты и заметки

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

Атрибуты не поддерживаются C# сценарием.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Атрибуты не поддерживаются в JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Атрибуты не поддерживаются в Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Аннотация `@TimerTrigger` для функции определяет расписание, используя тот же формат строки, что и [выражения CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

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

---

## <a name="configuration"></a>Настройка

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `TimerTrigger`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Н/Д | Этому свойству необходимо присвоить значение "timerTrigger". Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Н/Д | Для этого свойства необходимо задать значение "in". Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Н/Д | Имя переменной, представляющей объект таймера в коде функции. | 
|**schedule**|**ScheduleExpression**|Значение [выражения CRON](#ncrontab-expressions) или [TimeSpan](#timespan). `TimeSpan` можно использовать только для приложения-функции, которая работает в плане службы приложений. Вы можете поместить выражение расписания в параметр приложения и присвоить этому свойству имя параметра приложения, заключенное в знаки **%** , например: "%ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Если настроено значение `true`, функция вызывается при запуске среды выполнения. Например, среда выполнения запускается, когда приложение-функция выходит из спящего режима (в который она перешла из-за отсутствия активности), При перезапуске приложения-функции в связи с изменением функции и при масштабировании приложения-функции. Так что **рунонстартуп** следует использовать редко, если всегда имеет значение `true`, особенно в рабочей среде. |
|**useMonitor**|**UseMonitor**|Установите значение `true` или`false`, чтобы указать, следует ли отслеживать расписание. При мониторинге расписания его экземпляры сохраняются, чтобы обеспечить его корректную обработку даже при перезапуске экземпляров приложения-функции. Если значение не задано явно, по умолчанию используется `true` для расписаний, для которых интервал повторения больше или равен 1 минуте. Для расписаний, выполняющихся чаще одного раза в минуту, значением по умолчанию является `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Мы не рекомендуем задавать параметр **runOnStartup** для `true` в рабочей среде. Использование этого параметра заставляет код выполняться в очень непредсказуемое время. В определенных рабочих ситуациях эти дополнительные выполнения могут привести значительно более высоким затратам для приложений, размещенных в планах потребления. Например, при включенном параметре **рунонстартуп** триггер вызывается при каждом масштабировании приложения-функции. Убедитесь, что вы полностью понимаете рабочее поведение своих функций перед включением **runOnStartup** в рабочей среде.   

## <a name="usage"></a>Использование

При вызове функции триггера таймера в функцию передается объект Timer. Далее представлен JSON в качестве примера объекта таймера.

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

## <a name="ncrontab-expressions"></a>Выражения НКРОНТАБ 

Функции Azure используют библиотеку [нкронтаб](https://github.com/atifaziz/NCrontab) для интерпретации выражений нкронтаб. Выражение НКРОНТАБ аналогично выражению CRON, за исключением того, что оно включает в начало в начале использования для точности времени в секундах дополнительное шестое поле.

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

### <a name="ncrontab-examples"></a>Примеры НКРОНТАБ

Ниже приведены некоторые примеры выражений НКРОНТАБ, которые можно использовать для триггера таймера в функциях Azure.

|Пример|Когда активируется  |
|---------|---------|
|`"0 */5 * * * *"`|через каждые пять минут|
|`"0 0 * * * *"`|через каждый час|
|`"0 0 */2 * * *"`|через каждые 2 часа|
|`"0 0 9-17 * * *"`|один раз в час с 9:00 до 17:00|
|`"0 30 9 * * *"`|каждый день в 9:30|
|`"0 30 9 * * 1-5"`|каждый рабочий день в 9:30|
|`"0 30 9 * Jan Mon"`|в 9:30 каждый понедельник в январе|


### <a name="ncrontab-time-zones"></a>Часовые пояса НКРОНТАБ

Числа в выражении CRON представляют собой время и дату, а не временной интервал. Например, значение 5 в поле `hour` означает 5:00, а не каждые 5 часов.

Часовой пояс по умолчанию, используемый с выражениями CRON — время в формате UTC. Если нужно использовать другой часовой пояс в выражении CRON, создайте параметр приложения с именем `WEBSITE_TIME_ZONE` для приложения-функции. В качестве значения задайте имя нужного часового пояса, как показано в статье [Microsoft Time Zone Index](https://technet.microsoft.com/library/cc749073) (Индексы часовых поясов Майкрософт).

  > [!NOTE]
  > `WEBSITE_TIME_ZONE` в настоящее время не поддерживается в плане потребления Linux.

Например, *восточное поясное время* — UTC-05:00. Чтобы триггер таймера активировался в 10:00 часа EST ежедневно, используйте следующее выражение НКРОНТАБ, которое используется для часового пояса UTC:

```
"0 0 15 * * *"
``` 

Или создайте параметр приложения с именем `WEBSITE_TIME_ZONE` для приложения-функции и задайте **Восточное время США** в качестве значения.  Затем использует следующее выражение НКРОНТАБ: 

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
|"24:00:00" | каждые 24 часа        |
|"1,00:00:00" | Каждый день        |

## <a name="scale-out"></a>Расширение

Если приложение-функция будет развернуто на несколько экземпляров, то только один из экземпляров активируемой по таймеру функции выполняется для всех экземпляров.

## <a name="function-apps-sharing-storage"></a>Совместное использование хранилища приложениями-функциями

При совместном использовании учетных записей хранения в приложениях функций, которые не развернуты в службе приложений, может потребоваться явно назначить идентификатор узла каждому приложению.

| Версия службы "Функции" | Параметр                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x               | переменная среды `AzureFunctionsWebHost__hostid` |
| 1.x               | `id` в *Host. JSON*                                  |

Можно опустить идентифицирующее значение или вручную задать для каждой идентифицирующей конфигурации приложения-функции другое значение.

Триггер таймера использует блокировку хранилища, чтобы гарантировать наличие только одного экземпляра таймера, когда приложение-функция масштабируется до нескольких экземпляров. Если два приложения-функции совместно используют одну и ту же идентифицирующую конфигурацию, и каждый из них использует триггер таймера, запускается только один таймер.

## <a name="retry-behavior"></a>Поведение при повторе

В отличие от триггера очереди триггер таймера не осуществляет повторную попытку после того, как произошла ошибка выполнения функции. Когда функция возвращает ошибку, следующий раз она будет вызвана только по расписанию.

## <a name="troubleshooting"></a>Устранение неисправностей

Дополнительные сведения о том, что делать, когда триггер таймера неисправен, см. в разделе [Расследование проблем, когда функции, вызываемые таймером, не срабатывают](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Перейдите к краткому руководству по использованию триггера таймера](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
