---
title: Справочник разработчика для языка F# по Функциям Azure | Документация Майкрософт
description: Узнайте, как разрабатывать функции Azure с помощью скрипта F#.
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
keywords: служба "Функции Azure", функции, обработка событий, веб-перехватчики, динамические вычисления, бессерверная архитектура, F#
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: azure-functions
ms.devlang: fsharp
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 981ffce34c56f4becee2ed0c72da72baa220e395
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894361"
---
# <a name="azure-functions-f-developer-reference"></a>Справочник разработчика F# по Функциям Azure

F# для Функций Azure — это решение для быстрого запуска фрагментов кода (функций) в облаке. Данные поступают в функцию F# через аргументы функции. Имена аргументов указываются в `function.json`, и есть предварительно определенные имена для доступа к таким объектам, как средство ведения журнала функций и маркеры отмены. 

>[!IMPORTANT]
>Скрипт F# (.fsx) поддерживается только в среде выполнения функций Azure [версии 1.x](functions-versions.md#creating-1x-apps). Чтобы использовать F# в среде выполнения версии 2.x, необходимо использовать предварительно скомпилированный проект библиотеки классов F# (с расширением .fs). Создание, управление и публикация проекта библиотеки классов F# выполняются в Visual Studio точно так же, как и [проекта библиотеки классов C#](functions-dotnet-class-library.md). Дополнительные сведения о версиях службы "Функции Azure" см. [здесь](functions-versions.md).

В этой статье предполагается, что вы уже прочли [справочник разработчика по Функциям Azure](functions-reference.md).

## <a name="how-fsx-works"></a>Принципы работы FSX-файла
Файл `.fsx` — это скрипт F#. Он может рассматриваться как проект F#, содержащийся в одном файле. Этот файл содержит код для программы (в этом случае функция Azure) и директивы для управления зависимостями.

При использовании `.fsx` для функции Azure обычно необходимые сборки добавляются автоматически, что позволяет сосредоточиться на функции, а не на стандартном коде.

## <a name="folder-structure"></a>Структура папок

Структура папок для проекта на F# выглядит следующим образом.

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Существует общий файл [host.json](functions-host-json.md), который можно использовать для настройки приложения-функции. У каждой функции есть собственный файл кода (.fsx) и файл конфигурации привязки (function.json).

Расширения привязки, необходимые в [версии 2.x](functions-versions.md) среды выполнения функций, определены в файле `extensions.csproj` с фактическими файлами библиотеки в папке `bin`. При локальной разработке необходимо [зарегистрировать расширения привязки](./functions-bindings-register.md#local-development-azure-functions-core-tools). При разработке функций на портале Azure эта регистрация выполняется автоматически.

## <a name="binding-to-arguments"></a>Привязка к аргументам
Каждая привязка поддерживает набор аргументов. Это подробно описано в [справочнике разработчика по триггерам и привязкам в Функциях Azure](functions-triggers-bindings.md). Например, одной из привязок аргументов, поддерживаемых триггером больших двоичных объектов, выступает POCO. Эту привязку можно представить с помощью записи F#. Например: 

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Для функции Azure на F# понадобится один или несколько аргументов. Говоря об аргументах Функций Azure, мы имеем в виду *входные* и *выходные* аргументы. Входной аргумент соответствует своему названию, так как это входные данные для функции Azure F#. *Выходной* аргумент представлен изменяемыми данными или аргументом `byref<>`, который используется для передачи данных обратно *из* этой функции.

В приведенном выше примере `blob` выступает в качестве входного аргумента, а `output` — выходного аргумента. Обратите внимание, мы использовали `byref<>` для `output` (не нужно добавлять аннотацию `[<Out>]`). При использовании типа `byref<>` функция может изменять запись или объект, на который будет ссылаться аргумент.

Если в качестве типа входных данных используется запись F#, определение записи необходимо пометить атрибутом `[<CLIMutable>]` , чтобы платформа Функций Azure могла установить поля соответствующим образом перед передачей записи в функцию. "За кулисами" `[<CLIMutable>]` создает методы задания свойств записи. Например: 

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Класс F# можно также использовать для входных и выходных аргументов. Для свойств класса обычно требуются методы получения и задания. Например: 

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Ведение журналов
Для записи выходных данных в [потоковые журналы](../app-service/troubleshoot-diagnostic-logs.md) в F# в функции следует использовать аргумент типа [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Для согласованности мы советуем назвать этот аргумент `log`. Например: 

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Асинхронный режим
Рабочий процесс `async` можно использовать, но результат должен возвратить `Task`. Для этого нужно использовать `Async.StartAsTask`. Например:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Токен отмены
Если функция должна правильно обрабатывать завершение работы, ей можно присвоить аргумент [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken). При этом можно использовать `async`. Например:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Импорт пространств имен
Пространства имен можно открывать в обычном режиме.

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Следующие пространства имен открываются автоматически:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Ссылки на внешние сборки
Ссылки на сборку платформы можно добавить с помощью директивы `#r "AssemblyName"`.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Следующие сборки автоматически добавляются средой внешнего размещения Функций Azure:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Кроме того, следующие сборки представляют собой частные случаи, и к ним можно обращаться по простому имени (например, `#r "AssemblyName"`).

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Если необходимо указать ссылку на закрытую сборку, можно отправить файл сборки в папку `bin`, связанную с функцией, и указать его по имени файла (например, `#r "MyAssembly.dll"`). Дополнительные сведения о передаче файлов в папку функции см. в следующем разделе.

## <a name="editor-prelude"></a>Вводная часть редактора
В редакторе, поддерживающем службы компиляции F#, не будут учитываться пространства имен и сборки, которые Функции Azure автоматически включают в себя. Таким образом, может быть полезно включить вводную часть, которая помогает редактору найти используемые сборки и явным образом открыть пространства имен. Например: 

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

При выполнении кода Функции Azure обрабатывают источник, для которого определено `COMPILED`. Таким образом, вводная часть редактора будет игнорироваться.

<a name="package"></a>

## <a name="package-management"></a>Управление пакетами
Чтобы использовать пакеты NuGet в функции F#, добавьте файл `project.json` в папку соответствующей функции в файловой системе приложения-функции. Ниже приведен пример файла `project.json`, который добавляет ссылку на пакет NuGet в `Microsoft.ProjectOxford.Face` версии 1.1.0.

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Поддерживается только версия .NET Framework 4.6, поэтому убедитесь, что ваш файл `project.json` определяет `net46`, как показано ниже.

При отправке файла `project.json` среда выполнения получает пакеты и автоматически добавляет ссылки на сборки пакетов. Добавлять директивы `#r "AssemblyName"` не нужно. Просто добавьте необходимые операторы `open` в свой файл `.fsx`.

Может потребоваться автоматически поместить ссылки на сборки во вводную часть редактора, чтобы улучшить взаимодействие редактора со службами компиляции F#.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Добавление файла `project.json` в функцию Azure
1. Сначала убедитесь, что приложение функции запущено, для чего можно открыть эту функцию на портале Azure. Это также обеспечивает доступ к потоковым журналам, где будут отображаться выходные данные установки пакета.
2. Чтобы отправить файл `project.json` , воспользуйтесь одним из методов, описанных в разделе [Как обновить файлы приложения-функции](functions-reference.md#fileupdate). При использовании [непрерывного развертывания для Функций Azure](functions-continuous-deployment.md) можно добавить файл `project.json` в промежуточную ветвь, чтобы поэкспериментировать с ним, прежде чем добавлять в ветвь развертывания.
3. После добавления файла `project.json` в потоковом журнале функции отобразятся выходные данные, как в следующем примере.

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Переменные среды
Чтобы получить значение переменной среды или значение параметра приложения, используйте свойство `System.Environment.GetEnvironmentVariable`, как в следующем примере:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Повторное использование кода FSX-файла
Вы можете использовать код из других файлов `.fsx` с помощью директивы `#load`. Например: 

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Пути, передаваемые в директиве `#load`, определены относительно расположения вашего файла `.fsx`.

* `#load "logger.fsx"` загружает файл, расположенный в папке функции.
* `#load "package\logger.fsx"` загружает файл, расположенный в папке `package`, которая содержится в папке функции.
* `#load "..\shared\mylogger.fsx"` загружает файл, расположенный в папке `shared` на том же уровне, что и папка функции, то есть непосредственно в разделе `wwwroot`.

Директива `#load` работает только с файлами `.fsx` (скрипт F#), а не с файлами `.fs`.

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительных сведений см. следующие ресурсы:

* [Руководство по F#](/dotnet/articles/fsharp/index)
* [Best Practices for Azure Functions](functions-best-practices.md) (Рекомендации по Функциям Azure)
* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)
* [Тестирование Функций Azure](functions-test-a-function.md)
* [Масштабирование Функций Azure](functions-scale.md)

