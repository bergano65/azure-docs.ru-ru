---
title: Цепочки функций в устойчивых функциях (Azure)
description: Ознакомьтесь с примером устойчивой функции, которая выполняет последовательность функций.
author: cgillum
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: e8c314b6288bc26ad48fd210e866b2b67e433e17
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231326"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Цепочки функций в устойчивых функциях — пример последовательности Hello

Цепочкой функций называют схему выполнения последовательности функций в определенном порядке. Часто требуется передать выходные данные одной функции во входные данные другой. В этой статье описываются цепочки последовательности, создаваемой по завершении "Краткого руководства по Устойчивым функциям" (для [C#](durable-functions-create-first-csharp.md) или [JavaScript](quickstart-js-vscode.md)). Дополнительные сведения об Устойчивых функций см. в статье, посвященной [обзору Устойчивых функций](durable-functions-overview.md).

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Функции

В этой статье описаны следующие функции в примере приложения:

* Функция оркестратора `E1_HelloSequence`, которая вызывает `E1_SayHello` несколько раз подряд. При этом сохраняются выходные данные каждого вызова `E1_SayHello` и записываются результаты.
* Функция действия `E1_SayHello`, которая добавляет Hello в начало строки.

В следующих разделах описывается конфигурация и код, используемые для C# написания сценариев и JavaScript. Код для разработки с помощью Visual Studio представлен в конце этой статьи.

> [!NOTE]
> Устойчивые функции JavaScript доступны только для среды выполнения функций 2,0.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>Файл function.json

Если вы используете для разработки Visual Studio Code или портал Azure, файл *function.json* с указанным здесь содержимым позволит создать функцию оркестратора. Для большинства функций оркестратора файл *function.json* будет выглядеть почти так же.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Самое важное здесь — это тип привязки `orchestrationTrigger`. Во всех функциях оркестратора должен использоваться такой тип триггера.

> [!WARNING]
> Чтобы не нарушать требование "без ввода и вывода", применяемое к функциям оркестратора, не используйте никакие привязки входных или выходных данных совместно с триггером привязки `orchestrationTrigger`.  Если вам нужны входные или выходные привязки, используйте их в контексте функций `activityTrigger`, которые вызываются оркестратором. Дополнительные сведения см. в статье [ограничения кода функции Orchestrator](durable-functions-code-constraints.md) .

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>Скрипт C# (пример кода Visual Studio Code и портала Azure)

Ниже приведен исходный код:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Для всех функций оркестратора на C# должен использоваться параметр типа `DurableOrchestrationContext`, который присутствует в сборке `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Если вы работаете со скриптом C#, для ссылки на сборку можно использовать нотацию `#r`. Этот объект контекста позволяет вызывать другие функции *действий* и передавать входные параметры с помощью метода `CallActivityAsync`.

Этот код трижды последовательно вызывает `E1_SayHello` с разными значениями параметров. Значение, возвращаемое при каждом вызове, добавляется в список `outputs`, который возвращается в качестве результата функции.

### <a name="javascript"></a>JavaScript

Ниже приведен исходный код:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Все функции оркестратора на JavaScript должны содержать модуль [`durable-functions` ](https://www.npmjs.com/package/durable-functions). Это библиотека, позволяющая писать Устойчивые функции JavaScript. Есть три существенных различия между функциями оркестратора и другими функциями JavaScript:

1. Эта функция является [функцией генератора](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript).
2. Эта функция упаковывается в вызов к методу `durable-functions` модуля `orchestrator` (в нашем примере это `df`).
3. Функции должны быть синхронными. Так как метод orchestrator обрабатывает вызов аргумента context.done, функция должна возвращать значение.

Объект `context` содержит объект `df`, что позволяет вызывать другие функции *действий* и передавать им входные параметры с помощью метода `callActivity`. Этот код вызывает `E1_SayHello` три раза подряд с разными значениями параметров, указывая с помощью `yield`, что процесс выполнения должен ожидать возврата из асинхронных функций действий. Значение, возвращаемое при каждом вызове, добавляется в список `outputs`, который возвращается в качестве результата функции.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>Файл function.json

Файл *Function.json* для функции действия `E1_SayHello` будет таким же, как и для `E1_HelloSequence`, с одним исключением: в нем используется тип привязки `activityTrigger` вместо `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Для любой функции, которую вызывает функция оркестратора, должна использоваться привязка `activityTrigger`.

Функция `E1_SayHello` реализует достаточно простую операцию форматирования строки.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Эта функция имеет параметр типа `DurableActivityContext`, который используется для получения входных данных, переданных в нее с помощью вызова функции Orchestrator `CallActivityAsync<T>`.

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

В отличие от функции оркестратора JavaScript, функция действия не требует дополнительной настройки. Входные данные, которые ей передает функция оркестратора, размещаются в объекте `context.bindings` под именем привязки `activityTrigger` (в нашем примере это `context.bindings.name`). Имя привязки можно задать в качестве параметра экспортируемой функции и обращаться к нему напрямую, как показано в этом примере кода.

## <a name="run-the-sample"></a>Запуск примера

Чтобы выполнить функцию оркестратора `E1_HelloSequence`, отправьте указанный ниже запрос HTTP POST.

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
> Конечная точка HTTP POST, которая запустила функцию оркестратора, реализована в этом примере как функция с именем HttpStart и активацией по HTTP-запросу. Вы можете использовать аналогичную логику запуска и для других типов триггеров, например `queueTrigger`, `eventHubTrigger` или `timerTrigger`.

Просмотрите журналы выполнения функции. Функция `E1_HelloSequence` запущена и выполнена несколько раз из-за поведения воспроизведения, описанного в разделе [надежность оркестрации](durable-functions-orchestrations.md#reliability) . С другой стороны, функция `E1_SayHello` выполнялась только три раза, поскольку для таких процессов логика повторов не применяется.

## <a name="visual-studio-sample-code"></a>Пример кода для Visual Studio

Пример описанной оркестрации, реализованной в одном файле C# в проекте Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Дополнительная информация

В этом примере показана простая оркестрация с цепочкой функций. В приведенном ниже примере кода показана реализация шаблона развертывания и объединения.

> [!div class="nextstepaction"]
> [Выполните пример с размножением и разветвлением](durable-functions-cloud-backup.md)
