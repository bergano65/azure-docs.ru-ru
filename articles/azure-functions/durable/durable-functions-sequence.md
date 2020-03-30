---
title: Цепочки функций в устойчивых функциях (Azure)
description: Ознакомьтесь с примером устойчивой функции, которая выполняет последовательность функций.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562073"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Цепочки функций в устойчивых функциях — пример последовательности Hello

Цепочкой функций называют схему выполнения последовательности функций в определенном порядке. Часто требуется передать выходные данные одной функции во входные данные другой. В этой статье описываются цепочки последовательности, создаваемой по завершении "Краткого руководства по Устойчивым функциям" (для [C#](durable-functions-create-first-csharp.md) или [JavaScript](quickstart-js-vscode.md)). Дополнительные сведения об Устойчивых функций см. в статье, посвященной [обзору Устойчивых функций](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Функции

В этой статье описаны следующие функции в примере приложения:

* `E1_HelloSequence`: [Функция оркестратора,](durable-functions-bindings.md#orchestration-trigger) которая вызывает `E1_SayHello` несколько раз в последовательности. При этом сохраняются выходные данные каждого вызова `E1_SayHello` и записываются результаты.
* `E1_SayHello`: [Функция активности,](durable-functions-bindings.md#activity-trigger) которая готовит строку с "Hello".
* `HttpStart`: Функция срабатывания HTTP, которая запускает экземпляр оркестратора.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence функция оркестратора

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Для всех функций оркестратора на C# должен использоваться параметр типа `DurableOrchestrationContext`, который присутствует в сборке `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Этот объект контекста *activity* позволяет вызывать другие функции `CallActivityAsync` активности и передавать параметры ввода с помощью своего метода.

Этот код трижды последовательно вызывает `E1_SayHello` с разными значениями параметров. Значение, возвращаемое при каждом вызове, добавляется в список `outputs`, который возвращается в качестве результата функции.

# <a name="javascript"></a>[Javascript](#tab/javascript)

> [!NOTE]
> Долговременные функции JavaScript доступны только для выполнения функций 2.0.

#### <a name="functionjson"></a>function.json

Если вы используете для разработки Visual Studio Code или портал Azure, файл *function.json* с указанным здесь содержимым позволит создать функцию оркестратора. Для большинства функций оркестратора файл *function.json* будет выглядеть почти так же.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Самое важное здесь — это тип привязки `orchestrationTrigger`. Во всех функциях оркестратора должен использоваться такой тип триггера.

> [!WARNING]
> Чтобы не нарушать требование "без ввода и вывода", применяемое к функциям оркестратора, не используйте никакие привязки входных или выходных данных совместно с триггером привязки `orchestrationTrigger`.  Если вам нужны входные или выходные привязки, используйте их в контексте функций `activityTrigger`, которые вызываются оркестратором. Для получения дополнительной [orchestrator function code constraints](durable-functions-code-constraints.md) информации см.

#### <a name="indexjs"></a>Файл index.js

Вот функция:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Все функции оркестровки JavaScript должны включать [ `durable-functions` модуль.](https://www.npmjs.com/package/durable-functions) Это библиотека, которая позволяет писать функции длительного в JavaScript. Есть три существенных различия между функциями оркестратора и другими функциями JavaScript:

1. Функция генератора [функции.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript).
2. Эта функция упаковывается в вызов к методу `orchestrator` модуля `durable-functions` (в нашем примере это `df`).
3. Функции должны быть синхронными. Так как метод orchestrator обрабатывает вызов аргумента context.done, функция должна возвращать значение.

Объект `context` содержит `df` прочный объект контекста оркестровки, который позволяет вызывать `callActivity` другие функции *активности* и передавать параметры ввода с помощью своего метода. Этот код вызывает `E1_SayHello` три раза подряд с разными значениями параметров, указывая с помощью `yield`, что процесс выполнения должен ожидать возврата из асинхронных функций действий. Значение возврата каждого вызова добавляется `outputs` в массив, который возвращается в конце функции.

---

### <a name="e1_sayhello-activity-function"></a>функция активности E1_SayHello

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Действия используют `ActivityTrigger` атрибут. Используйте `IDurableActivityContext` предоставленное для выполнения действий, связанных `GetInput<T>`с действиями, такими как доступ к входной стоимости с использованием.

Функция `E1_SayHello` реализует достаточно простую операцию форматирования строки.

Вместо `IDurableActivityContext`того, чтобы связываться с, вы можете связываться непосредственно с типом, который передается в функцию действия. Пример:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.json

Файл *Function.json* для функции действия `E1_SayHello` будет таким же, как и для `E1_HelloSequence`, с одним исключением: в нем используется тип привязки `activityTrigger` вместо `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Для любой функции, которую вызывает функция оркестратора, должна использоваться привязка `activityTrigger`.

Функция `E1_SayHello` реализует достаточно простую операцию форматирования строки.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

В отличие от функции оркестратора JavaScript, функция действия не требует дополнительной настройки. Входные данные, которые ей передает функция оркестратора, размещаются в объекте `context.bindings` под именем привязки `activityTrigger` (в нашем примере это `context.bindings.name`). Имя привязки можно задать в качестве параметра экспортируемой функции и обращаться к нему напрямую, как показано в этом примере кода.

---

### <a name="httpstart-client-function"></a>Функция клиента HttpStart

Вы можете запустить экземпляр функции оркестратора с помощью клиентской функции. Вы будете `HttpStart` использовать функцию срабатывания HTTP для запуска `E1_HelloSequence`экземпляров.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Чтобы взаимодействовать с оркестрантами, `DurableClient` функция должна включать вхотовый переплет. Вы используете клиента для запуска оркестровки. Он также может помочь вам вернуть ответ HTTP, содержащий URL-адреса для проверки состояния новой оркестровки.

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Чтобы взаимодействовать с оркестрантами, `durableClient` функция должна включать вхотовый переплет.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Используйте `df.getClient` для `DurableOrchestrationClient` получения объекта. Вы используете клиента для запуска оркестровки. Он также может помочь вам вернуть ответ HTTP, содержащий URL-адреса для проверки состояния новой оркестровки.

---

## <a name="run-the-sample"></a>Запуск примера

Чтобы выполнить согласование, `E1_HelloSequence` отправьте следующий запрос HTTP POST на функцию. `HttpStart`

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

Просмотрите журналы выполнения функции. Функция `E1_HelloSequence` запускалась и завершается несколько раз из-за поведения воспроизведения, описанного в теме [надежности оркестровки.](durable-functions-orchestrations.md#reliability) С другой стороны, функция `E1_SayHello` выполнялась только три раза, поскольку для таких процессов логика повторов не применяется.

## <a name="next-steps"></a>Дальнейшие действия

В этом примере показана простая оркестрация с цепочкой функций. В приведенном ниже примере кода показана реализация шаблона развертывания и объединения.

> [!div class="nextstepaction"]
> [Выполните пример с размножением и разветвлением](durable-functions-cloud-backup.md)
