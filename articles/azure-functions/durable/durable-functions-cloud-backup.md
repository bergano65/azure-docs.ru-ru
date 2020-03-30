---
title: Сценарии развертывания и объединения в устойчивых функциях — Azure
description: Сведения о том, как реализовать сценарии развертывания и объединения в расширении устойчивых функций для Функций Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562196"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Сценарии развертывания и объединения в устойчивых функциях. Пример резервного копирования в облако

*Развертывание и объединение* — это шаблон параллельного выполнения нескольких функций с последующим статистическим вычислением результатов. В этой статье описывается пример, использующий [устойчивые функции](durable-functions-overview.md) для реализации сценариев развертывания и объединения. Образец представляет устойчивую функцию, создающую резервную копию всего или некоторого содержимого сайта приложения в службе хранилища Azure.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Обзор сценария

В этом образце функции рекурсивно отправляют все файлы в указанном каталоге в хранилище BLOB-объектов и подсчитывают общее число отправленных байтов.

Можно написать одну функцию, которая отвечает за все. Основная проблема, которая может возникнуть, связана с выполнением **масштабируемости**. Одно функциональное выполнение может выполняться только на одной виртуальной машине, поэтому пропускная стоимость будет ограничена пропускной стоимостью одного VM. Следующая проблема заключается в **надежности**. Если произошел сбой в середине процесса или весь процесс занимает более 5 минут, резервное копирование может выполнить неудачу в частично завершенном состоянии. В таком случае его необходимо начать сначала.

Более надежным подходом было бы записать две обычные функции: одна для перечисления файлов и добавления их имен в очередь, а другая для чтения из очереди и отправки файлов в хранилище BLOB-объектов. Этот подход лучше с точки зрения пропускной связи и надежности, но он требует от вас резервировать и управлять очередью. Если требуется выполнить какое-либо действие (например, сообщить об общем количестве отправленных байтов), могут возникнуть значительные проблемы в контексте **управления состоянием** и **координацией**.

Подход к устойчивым функциям обеспечивает получение всех упомянутых преимуществ с очень низкими издержками.

## <a name="the-functions"></a>Функции

В этой статье описаны следующие функции в примере приложения:

* `E2_BackupSiteContent`: [Функция оркестратора,](durable-functions-bindings.md#orchestration-trigger) которая вызывает `E2_GetFileList` для получения списка файлов `E2_CopyFileToBlob` для резервного копирования, а затем вызывает для резервного копирования каждого файла.
* `E2_GetFileList`: [Функция активности,](durable-functions-bindings.md#activity-trigger) которая возвращает список файлов в каталоге.
* `E2_CopyFileToBlob`: Функция активности, которая резервирует один файл в хранилище Azure Blob.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent функция оркестратора

Эта функция оркестратора выполняет следующие задачи:

1. Принимает значение `rootDirectory` в качестве входного параметра.
2. Вызывает функцию для получения рекурсивного списка файлов в `rootDirectory`.
3. Выполняет несколько параллельных вызовов функции для отправки каждого файла в хранилище BLOB-объектов Azure.
4. Ожидает завершения всех передач.
5. Возвращает сумму общего количества байтов, отправленных в хранилище BLOB-объектов Azure.

# <a name="c"></a>[C #](#tab/csharp)

Ниже приведен код, реализующий функцию оркестратора.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Обратите внимание на строку `await Task.WhenAll(tasks);`. Все индивидуальные вызовы на функцию `E2_CopyFileToBlob` *не* ждали, что позволяет им работать параллельно. При передаче массива задач в `Task.WhenAll` мы получаем задачу, которая не выполнится *до завершения всех операций копирования*. Если вы знакомы с библиотекой параллельных задач (TPL) в .NET, то вы уже знаете об этом. Разница в том, что эти задачи могут выполняться одновременно на нескольких виртуальных машинах, а расширение функций durable functions гарантирует, что сквозное выполнение является устойчивым к переработке процессов.

Если задача `Task.WhenAll` завершена, это говорит о том, что все вызовы функций завершены и значения возвращены. Каждый вызов `E2_CopyFileToBlob` возвращает число переданных байтов, поэтому, чтобы узнать сумму всех байтов, нужно сложить все возвращаемые значения.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Функция использует стандартную *функцию.json* для функций оркестратора.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Ниже приведен код, реализующий функцию оркестратора.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Обратите внимание на строку `yield context.df.Task.all(tasks);`. Все индивидуальные вызовы к функции `E2_CopyFileToBlob` *не* были получены, что позволяет им работать параллельно. При передаче массива задач в `context.df.Task.all` мы получаем задачу, которая не выполнится *до завершения всех операций копирования*. Если вы знакомы [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) с JavaScript, то это не ново для вас. Разница в том, что эти задачи могут выполняться одновременно на нескольких виртуальных машинах, а расширение функций durable functions гарантирует, что сквозное выполнение является устойчивым к переработке процессов.

> [!NOTE]
> Несмотря на то что задачи концептуально похожи на обещания JavaScript, функции оркестратора должны использовать `context.df.Task.all` и `context.df.Task.any` вместо `Promise.all` и `Promise.race` для управления параллелизацией задач.

После выхода `context.df.Task.all`из , мы знаем, что все вызовы функции завершили и вернули значения обратно к нам. Каждый вызов `E2_CopyFileToBlob` возвращает число переданных байтов, поэтому, чтобы узнать сумму всех байтов, нужно сложить все возвращаемые значения.

---

### <a name="helper-activity-functions"></a>Вспомогательные функции действий

Вспомогательные функции действий, как и другие примеры, являются обычными функциями, которые используют привязку триггера `activityTrigger`.

#### <a name="e2_getfilelist-activity-function"></a>функция активности E2_GetFileList

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

Файл *function.json* `E2_GetFileList` для выглядит следующим образом:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

Вот сама реализация:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Функция использует `readdirp` модуль (версия 2.x) для повторного чтения структуры каталога.

---

> [!NOTE]
> Вы можете спросить, почему нельзя просто поместить этот код непосредственно в функцию оркестратора. Это действие может нарушить одно из основных правил функций оркестратора — они не должны выполнять операции ввода-вывода, включая доступ к локальной файловой системе. Для получения дополнительной [Orchestrator function code constraints](durable-functions-code-constraints.md)информации см.

#### <a name="e2_copyfiletoblob-activity-function"></a>функция E2_CopyFileToBlob деятельности

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> Для запуска примерного `Microsoft.Azure.WebJobs.Extensions.Storage` кода необходимо установить пакет NuGet.

Функция использует некоторые расширенные функции привязки функций Azure Functions (т.е. использование [ `Binder` параметра),](../functions-dotnet-class-library.md#binding-at-runtime)но вам не нужно беспокоиться об этих деталях для целей этого пошагового листа.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Файл *unction.json* для `E2_CopyFileToBlob` такой же простой:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

Реализация JavaScript использует [SDK хранилища Azure для Узлов для](https://github.com/Azure/azure-storage-node) загрузки файлов в Хранилище Azure Blob.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

Реализация загружает файл с диска и асинхронно выполняет потоковую передачу содержимого в одноименный большой двоичный объект в контейнере резервных копий. В результате возвращается количество байтов, скопированных в хранилище, которое затем используется функцией оркестратора для вычисления общей суммы.

> [!NOTE]
> Это отличный пример перемещения операций ввода-вывода в функцию `activityTrigger`. Не только работа может быть распределена по многим различным машинам, но вы также получите преимущества контрольной точки прогресса. Если хост-процесс по какой-либо причине завершится, вы будете знать, какие передачи уже выполнены.

## <a name="run-the-sample"></a>Запуск примера

Можно начать оркестрацию, отправив приведенный ниже запрос HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> Вызываемая функция `HttpStart` работает только с содержимым в формате JSON. По этой причине требуется заголовок `Content-Type: application/json` и путь к каталогу кодируется в виде строки JSON. Кроме того, в предыдущем фрагменте HTTP предполагается, что в файле `host.json` существует запись, которая позволяет удалить префикс `api/` по умолчанию из всех URL-адресов функций для триггеров HTTP. Разметку для этой конфигурации можно найти в файле `host.json` в примерах.

Этот запрос HTTP активирует оркестратор `E2_BackupSiteContent` и передает строку `D:\home\LogFiles` в качестве параметра. В ответе содержится ссылка на получение информации о состоянии операции резервного копирования:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

В зависимости от количества файлов журнала в приложении-функции, эта операция может занять несколько минут. Информацию об актуальном состоянии можно получить, отправив запрос на URL-адрес в заголовке `Location` предыдущего ответа HTTP 202.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

В этом случае функция все еще выполняется. Вы можете просмотреть входные данные, сохраненные в состоянии оркестратора, и последнее время обновления. Для опроса состояния завершения операции можно продолжать использовать значения заголовка `Location`. Если состояние — "Завершено", вы увидите HTTP-ответ, как в примере ниже:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Теперь оркестрация завершена, и вы можете оценить, сколько времени ушло на ее выполнение. Вы также увидите значение поля `output`, в котором указано, что отправлено примерно 450 КБ данных журналов.

## <a name="next-steps"></a>Дальнейшие действия

В этом примере показано, как реализовать шаблон развертывания и объединения. В следующем примере показано, как реализовать шаблон мониторинга с помощью [устойчивых таймеров](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Запуск примера мониторинга](durable-functions-monitor.md)