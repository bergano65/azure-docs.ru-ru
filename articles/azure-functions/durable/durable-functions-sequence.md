---
title: Цепочки функций в устойчивых функциях (Azure)
description: Ознакомьтесь с примером устойчивой функции, которая выполняет последовательность функций.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77562073"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Цепочки функций в устойчивых функциях — пример последовательности Hello

Цепочкой функций называют схему выполнения последовательности функций в определенном порядке. Часто требуется передать выходные данные одной функции во входные данные другой. В этой статье описываются цепочки последовательности, создаваемой по завершении "Краткого руководства по Устойчивым функциям" (для [C#](durable-functions-create-first-csharp.md) или [JavaScript](quickstart-js-vscode.md)). Дополнительные сведения об Устойчивых функций см. в статье, посвященной [обзору Устойчивых функций](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Функции

В этой статье описаны следующие функции в примере приложения:

* `E1_HelloSequence`: [Функция Orchestrator](durable-functions-bindings.md#orchestration-trigger) , которая вызывает `E1_SayHello` несколько раз в последовательности. При этом сохраняются выходные данные каждого вызова `E1_SayHello` и записываются результаты.
* `E1_SayHello`: [Функция действия](durable-functions-bindings.md#activity-trigger) , которая добавляет строку в начало строки с "Hello".
* `HttpStart`: Функция, активируемая по протоколу HTTP, которая запускает экземпляр Orchestrator.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence Orchestrator, функция

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Для всех функций оркестратора на C# должен использоваться параметр типа `DurableOrchestrationContext`, который присутствует в сборке `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Этот объект контекста позволяет вызывать другие функции *действий* и передавать входные параметры с помощью `CallActivityAsync` метода.

Этот код трижды последовательно вызывает `E1_SayHello` с разными значениями параметров. Значение, возвращаемое при каждом вызове, добавляется в список `outputs`, который возвращается в качестве результата функции.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> Устойчивые функции JavaScript доступны только для среды выполнения функций 2,0.

#### <a name="functionjson"></a>function.json

Если вы используете для разработки Visual Studio Code или портал Azure, файл *function.json* с указанным здесь содержимым позволит создать функцию оркестратора. Для большинства функций оркестратора файл *function.json* будет выглядеть почти так же.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Самое важное здесь — это тип привязки `orchestrationTrigger`. Во всех функциях оркестратора должен использоваться такой тип триггера.

> [!WARNING]
> Чтобы не нарушать требование "без ввода и вывода", применяемое к функциям оркестратора, не используйте никакие привязки входных или выходных данных совместно с триггером привязки `orchestrationTrigger`.  Если вам нужны входные или выходные привязки, используйте их в контексте функций `activityTrigger`, которые вызываются оркестратором. Дополнительные сведения см. в статье [ограничения кода функции Orchestrator](durable-functions-code-constraints.md) .

#### <a name="indexjs"></a>Файл index.js

Ниже приведена функция.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Все функции оркестрации JavaScript должны включать [ `durable-functions` модуль](https://www.npmjs.com/package/durable-functions). Это библиотека, позволяющая писать Устойчивые функции JavaScript. Есть три существенных различия между функциями оркестратора и другими функциями JavaScript:

1. Функция является [функцией генератора.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. Эта функция упаковывается в вызов к методу `orchestrator` модуля `durable-functions` (в нашем примере это `df`).
3. Функции должны быть синхронными. Так как метод orchestrator обрабатывает вызов аргумента context.done, функция должна возвращать значение.

`context` Объект содержит объект контекста `df` устойчивого оркестрации, который позволяет вызывать другие функции *действий* и передавать входные параметры с помощью `callActivity` метода. Этот код вызывает `E1_SayHello` три раза подряд с разными значениями параметров, указывая с помощью `yield`, что процесс выполнения должен ожидать возврата из асинхронных функций действий. Возвращаемое значение каждого вызова добавляется в `outputs` массив, который возвращается в конце функции.

---

### <a name="e1_sayhello-activity-function"></a>Функция действия E1_SayHello

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Действия используют `ActivityTrigger` атрибут. Используйте предоставленные `IDurableActivityContext` для выполнения действий, связанных с действиями, например для доступа к `GetInput<T>`входному значению с помощью.

Функция `E1_SayHello` реализует достаточно простую операцию форматирования строки.

Вместо привязки к `IDurableActivityContext`можно выполнить прямую привязку к типу, который передается в функцию действия. Пример:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/функтион.жсон

Файл *Function.json* для функции действия `E1_SayHello` будет таким же, как и для `E1_HelloSequence`, с одним исключением: в нем используется тип привязки `activityTrigger` вместо `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Для любой функции, которую вызывает функция оркестратора, должна использоваться привязка `activityTrigger`.

Функция `E1_SayHello` реализует достаточно простую операцию форматирования строки.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/индекс.ЖС

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

В отличие от функции оркестратора JavaScript, функция действия не требует дополнительной настройки. Входные данные, которые ей передает функция оркестратора, размещаются в объекте `context.bindings` под именем привязки `activityTrigger` (в нашем примере это `context.bindings.name`). Имя привязки можно задать в качестве параметра экспортируемой функции и обращаться к нему напрямую, как показано в этом примере кода.

---

### <a name="httpstart-client-function"></a>Функция клиента HttpStart

Вы можете запустить экземпляр функции Orchestrator с помощью клиентской функции. Для запуска экземпляров будет `HttpStart` использоваться функция, активируемая HTTP `E1_HelloSequence`.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Для взаимодействия с оркестрации функция должна включать `DurableClient` входную привязку. Для начала оркестрации используется клиент. Он также может помочь вернуть ответ HTTP, содержащий URL-адреса для проверки состояния нового оркестрации.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/Function. JSON

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Для взаимодействия с оркестрации функция должна включать `durableClient` входную привязку.

#### <a name="httpstartindexjs"></a>HttpStart/index. js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Используйте `df.getClient` для получения `DurableOrchestrationClient` объекта. Для начала оркестрации используется клиент. Он также может помочь вернуть ответ HTTP, содержащий URL-адреса для проверки состояния нового оркестрации.

---

## <a name="run-the-sample"></a>Запуск примера

Чтобы выполнить `E1_HelloSequence` согласование, отправьте в `HttpStart` функцию следующий запрос HTTP POST.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> В предыдущем фрагменте HTTP предполагается, что в файле `host.json` существует запись, которая позволяет удалить префикс `api/` по умолчанию из всех URL-адресов функций для триггеров HTTP. Разметку для этой конфигурации можно найти в файле `host.json` в примерах.

Скажем, если пример выполняется в приложении-функции с именем myfunctionapp, замените {host} значением myfunctionapp.azurewebsites.net.

Вы получите примерно такой ответ HTTP с кодом 202 (фрагмент ответа):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Функция оркестратора помещается в очередь и немедленно передается на выполнение. URL-адрес из заголовка `Location` позволяет проверить состояние выполнения функции.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

В ответе на такой запрос возвращаются сведения о состоянии оркестрации. Функция выполняется и завершается достаточно быстро, поэтому, вероятнее всего, отобразится состояние *Завершено* в ответе примерно такого вида (фрагмент ответа):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Как видите, параметр `runtimeStatus` для экземпляра имеет значение *Завершено*, а `output` содержит результат выполнения функции оркестратора, сериализованный в формате JSON.

> [!NOTE]
> Вы можете использовать аналогичную логику запуска и для других типов триггеров, например `queueTrigger`, `eventHubTrigger` или `timerTrigger`.

Просмотрите журналы выполнения функции. `E1_HelloSequence` Функция была запущена и выполнена несколько раз из-за поведения воспроизведения, описанного в разделе [надежность оркестрации](durable-functions-orchestrations.md#reliability) . С другой стороны, функция `E1_SayHello` выполнялась только три раза, поскольку для таких процессов логика повторов не применяется.

## <a name="next-steps"></a>Дальнейшие шаги

В этом примере показана простая оркестрация с цепочкой функций. В приведенном ниже примере кода показана реализация шаблона развертывания и объединения.

> [!div class="nextstepaction"]
> [Выполните пример с размножением и разветвлением](durable-functions-cloud-backup.md)
