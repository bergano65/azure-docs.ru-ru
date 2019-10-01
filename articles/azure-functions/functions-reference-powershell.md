---
title: Справочник разработчика PowerShell для функций Azure
description: Узнайте, как разрабатывать функции с помощью PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 36d24e798e73ef336324eedadee1ba3fec4c0e1d
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773030"
---
# <a name="azure-functions-powershell-developer-guide"></a>Рекомендации для разработчиков по функциям Azure PowerShell

Эта статья содержит сведения о том, как вы пишете функции Azure с помощью PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Функция Azure PowerShell (функция) представляется в виде скрипта PowerShell, который выполняется при срабатывании. Каждый сценарий функции имеет связанный `function.json` файл, который определяет, как работает функция, например, как она запускается, а также ее входные и выходные параметры. Дополнительные сведения см. в [статье о триггерах и привязке](functions-triggers-bindings.md). 

Как и другие типы функций, функции скриптов PowerShell принимают параметры, соответствующие именам всех входных привязок, определенных в `function.json` файле. Также `TriggerMetadata` передается параметр, содержащий дополнительные сведения о триггере, который запустил функцию.

В этой статье предполагается, что вы уже прочли [руководство для разработчиков по Функциям Azure](functions-reference.md). Кроме того, для создания первой функции PowerShell необходимо завершить работу с [кратким руководством по функциям PowerShell](functions-create-first-function-powershell.md) .

## <a name="folder-structure"></a>Структура папок

Необходимая структура папок для проекта PowerShell выглядит следующим образом. Это значение по умолчанию можно изменить. Дополнительные сведения см. в разделе о [scriptFile](#configure-function-scriptfile) ниже.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

В корне проекта есть общий [`host.json`](functions-host-json.md) файл, который можно использовать для настройки приложения-функции. У каждой функции есть папка с собственным файлом кода (PS1) и файл конфигурации привязки (`function.json`). Имя родительского каталога файла Function. JSON всегда является именем функции.

Для определенных привязок требуется наличие `extensions.csproj` файла. Расширения привязки, необходимые в [версии 2. x](functions-versions.md) среды выполнения функций, определяются в `extensions.csproj` файле с фактическими файлами `bin` библиотеки в папке. При локальной разработке необходимо [зарегистрировать расширения привязки](functions-bindings-register.md#extension-bundles). При разработке функций на портале Azure эта регистрация выполняется автоматически.

В приложениях- `profile.ps1` функциях PowerShell при необходимости можно использовать, который запускается при запуске приложения-функции (в противном случае — как *[холодный запуск](#cold-start)* ). Дополнительные сведения см. в разделе [профиль PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Определение скрипта PowerShell как функции

По умолчанию среда выполнения Функций ищет функцию в файле `run.ps1`, где `run.ps1` использует тот же родительский каталог, что и соответствующий файл `function.json`.

При выполнении скрипту передается ряд аргументов. Чтобы обрабатывал эти параметры, добавьте `param` блок в начало скрипта, как показано в следующем примере:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Тригжерметадата, параметр

`TriggerMetadata` Параметр используется для предоставления дополнительных сведений о триггере. Дополнительные метаданные отличаются от привязки к привязке, но все они содержат `sys` свойство, которое содержит следующие данные:

```powershell
$TriggerMetadata.sys
```

| Свойство   | Description                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Когда, в формате UTC, была активирована функция        | DateTime |
| MethodName | Имя функции, которая была активирована     | string   |
| рандгуид   | уникальный идентификатор GUID для этого выполнения функции | string   |

Каждый тип триггера имеет другой набор метаданных. Например, `$TriggerMetadata` для `QueueTrigger` `Id`содержит ,,`DequeueCount`и, помимо прочего,. `InsertionTime` Дополнительные сведения о метаданных триггера очереди см. в [официальной документации по триггерам очереди](functions-bindings-storage-queue.md#trigger---message-metadata). Ознакомьтесь с документацией по [триггерам](functions-triggers-bindings.md) , с которыми вы работаете, чтобы узнать, что входит в метаданные триггера.

## <a name="bindings"></a>Привязки

В PowerShell [привязки](functions-triggers-bindings.md) настраиваются и определяются в функции. JSON функции. Функции взаимодействуют с привязками несколькими способами.

### <a name="reading-trigger-and-input-data"></a>Чтение триггера и входных данных

Привязки триггера и ввода считываются как параметры, передаваемые в функцию. `direction` Для`in` входных привязок задано значение в Function. JSON. Свойство, определенное в `function.json` , является именем параметра в `param` блоке. `name` Так как PowerShell использует именованные параметры для привязки, порядок параметров не имеет значения. Однако рекомендуется следовать порядку привязок, определенных в `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Запись выходных данных

В функциях для `direction` `out` выходной привязки в Function. JSON задано значение. Запись в выходную привязку можно выполнить с помощью `Push-OutputBinding` командлета, который доступен для среды выполнения функций. Во всех случаях `name` свойство привязки, определенное в `function.json` , соответствует `Name` параметру `Push-OutputBinding` командлета.

Ниже показано, как вызвать `Push-OutputBinding` в скрипте функции:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Можно также передать значение для конкретной привязки через конвейер.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`ведет себя по-разному в зависимости от значения, указанного `-Name`для:

* Если указанное имя не может быть разрешено в допустимую выходную привязку, возникает ошибка.

* Если выходная привязка принимает коллекцию значений, для отправки нескольких значений можно `Push-OutputBinding` многократно вызывать несколько раз.

* Если выходная привязка принимает только одноэлементное значение, `Push-OutputBinding` вызов второго раза вызывает ошибку.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding`Syntax

Ниже приведены допустимые параметры для вызова `Push-OutputBinding`.

| Имя | Тип | Положение | Описание |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Строковое | 1 | Имя выходной привязки, которую необходимо задать. |
| **`-Value`** | Object | 2 | Значение выходной привязки, которое необходимо задать, которое принимается из Бивалуе конвейера. |
| **`-Clobber`** | SwitchParameter | именованная | Используемых При указании параметра устанавливает значение для заданной выходной привязки. | 

Также поддерживаются следующие общие параметры: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Дополнительные сведения см. в разделе [About общиепараметры](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Пример Push-Аутпутбиндинг: Ответы HTTP

Триггер HTTP возвращает ответ, используя выходную привязку с `response`именем. В следующем примере выходная привязка `response` имеет значение "Output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Поскольку выходным данным является HTTP, которая принимает только одноэлементное значение, возникает ошибка, когда `Push-OutputBinding` вызывается второй раз.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Для выходов, которые принимают только одноэлементные значения, `-Clobber` можно использовать параметр для переопределения старого значения вместо того, чтобы пытаться добавить его в коллекцию. В следующем примере предполагается, что вы уже добавили значение. Используя `-Clobber`, ответ из следующего примера переопределяет существующее значение для возврата значения "Output #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Пример Push-Аутпутбиндинг: Выходная привязка очереди

`Push-OutputBinding`используется для отправки данных в выходные привязки, такие как [выходная привязка хранилища очередей Azure](functions-bindings-storage-queue.md#output). В следующем примере сообщение, записанное в очередь, имеет значение "Output #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Выходная привязка для очереди хранилища принимает несколько выходных значений. В этом случае вызов следующего примера после первой записи в очередь списка с двумя элементами: "Output #1" и "Output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

В следующем примере, когда вызывается после двух предыдущих, в выходную коллекцию добавляется еще два значения:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

При запись в очередь сообщение содержит следующие четыре значения: "Output #1", "Output #2", "Output #3" и "Output #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`Командлет

С помощью `Get-OutputBinding` командлета можно получить значения, заданные в данный момент для выходных привязок. Этот командлет извлекает хэш-таблицу, содержащую имена выходных привязок с соответствующими значениями. 

Ниже приведен пример использования `Get-OutputBinding` для возврата текущих значений привязки.

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`также содержит параметр с именем `-Name`, который можно использовать для фильтрации возвращаемой привязки, как показано в следующем примере:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Подстановочные знаки (*) поддерживаются `Get-OutputBinding`в.

## <a name="logging"></a>Ведение журнала

Ведение журнала в функциях PowerShell работает как обычное ведение журнала PowerShell. Командлеты ведения журнала можно использовать для записи в каждый выходной поток. Каждый командлет сопоставляется с уровнем ведения журнала, который используется функциями.

| Уровень ведения журнала функций | Командлет ведения журнала |
| ------------- | -------------- |
| Ошибка | **`Write-Error`** |
| Предупреждение | **`Write-Warning`**  | 
| Сведения | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Сведения | Выполняет запись в журнал на уровне _информации_ . |
| Отладка | **`Write-Debug`** |
| Трассировка | **`Write-Progress`** <br /> **`Write-Verbose`** |

Помимо этих командлетов, все данные, записанные в конвейер, перенаправляются на `Information` уровень ведения журнала и отображаются с форматированием PowerShell по умолчанию.

> [!IMPORTANT]
> Использование командлетов `Write-Debug`илине является достаточным для просмотра подробных сведений и ведения журнала на уровне отладки. `Write-Verbose` Кроме того, необходимо настроить пороговое значение уровня ведения журнала, которое объявляет, какой уровень журналов вы в действительности интересуют. Дополнительные сведения см. в статье [Настройка уровня ведения журнала приложения функции](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Настройка уровня ведения журнала приложения функции

Функции Azure позволяют определить пороговый уровень, чтобы упростить управление способом записи функций в журналы. Чтобы задать пороговое значение для всех трассировок, записываемых на консоль `logging.logLevel.default` , используйте свойство [`host.json`в справочнике ][справочник по host. json]. Этот параметр применяется ко всем функциям в приложении-функции.

В следующем примере устанавливается пороговое значение для включения подробного ведения журнала для всех функций, но устанавливается пороговое значение включения ведения журнала `MyFunction`отладки для функции с именем:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Дополнительные сведения см. в [Справочник по Host. JSON].

### <a name="viewing-the-logs"></a>Просмотр журналов

Если приложение-функция выполняется в Azure, можно использовать Application Insights для мониторинга. Дополнительные сведения о просмотре журналов функций и обращении к ним см. в статье [мониторинг Функций Azure](functions-monitoring.md).

Если вы используете приложение-функция локально для разработки, ведет журнал по умолчанию в файловой системе. Чтобы просмотреть журналы в консоли, задайте `AZURE_FUNCTIONS_ENVIRONMENT` для `Development` переменной среды значение перед запуском приложение-функция.

## <a name="triggers-and-bindings-types"></a>Типы триггеров и привязок

Существует ряд триггеров и привязок, которые можно использовать с приложением-функцией. Полный список триггеров и привязок [можно найти здесь](functions-triggers-bindings.md#supported-bindings).

Все триггеры и привязки представлены в коде как несколько реальных типов данных:

* Hashtable
* string
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

Первые пять типов в этом списке являются стандартными типами .NET. Последние два используются только [триггером HttpTrigger](#http-triggers-and-bindings).

Каждый параметр привязки в функциях должен иметь один из этих типов.

### <a name="http-triggers-and-bindings"></a>Триггеры и привязки HTTP

Триггеры HTTP и webhook, а также привязки вывода HTTP используют объекты запроса и ответа для обмена сообщениями HTTP.

#### <a name="request-object"></a>Объект запроса

Объект запроса, переданный в скрипт, имеет тип `HttpRequestContext`, который имеет следующие свойства:

| Свойство  | Description                                                    | Тип                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Объект, содержащий текст запроса. `Body`сериализуется в лучший тип на основе данных. Например, если данные являются JSON, они передаются в виде хэш-таблицы. Если данные являются строкой, они передаются в виде строки. | object |
| **`Headers`** | Словарь, содержащий заголовки запроса.                | Строка < словаря, строка ><sup>*</sup> |
| **`Method`** | Метод HTTP, используемый для запроса.                                | string                    |
| **`Params`**  | Объект, содержащий параметры маршрутизации запроса. | Строка < словаря, строка ><sup>*</sup> |
| **`Query`** | Объект, содержащий параметры запроса.                  | Строка < словаря, строка ><sup>*</sup> |
| **`Url`** | URL-адрес запроса.                                        | string                    |

<sup>*</sup>Во `Dictionary<string,string>` всех ключах регистр не учитывается.

#### <a name="response-object"></a>Объект ответа

Объект ответа, который необходимо отправить обратно, имеет тип `HttpResponseContext`, который имеет следующие свойства:

| Свойство      | Description                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Объект, содержащий текст ответа.           | object                    |
| **`ContentType`** | Короткий рукой для установки типа содержимого для ответа. | string                    |
| **`Headers`** | Объект, содержащий заголовок ответа.               | Словарь или хэш-таблица   |
| **`StatusCode`**  | Код состояния HTTP ответа.                       | строка или целое число             |

#### <a name="accessing-the-request-and-response"></a>Доступ к запросу и ответу

При работе с триггерами HTTP доступ к HTTP-запросу можно получить так же, как и с любой другой входной привязкой. Он находится в `param` блоке.

`HttpResponseContext` Используйте объект для возврата ответа, как показано ниже:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

Результат вызова этой функции будет следующим:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Приведение типов для триггеров и привязок

Для некоторых привязок, таких как привязка больших двоичных объектов, можно указать тип параметра.

Например, чтобы данные из хранилища BLOB-объектов передавались в виде строки, добавьте следующий тип к моему `param` блоку:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Профиль PowerShell

В PowerShell есть понятие профиля PowerShell. Если вы не знакомы с профилями PowerShell, см. раздел [About Profiles](/powershell/module/microsoft.powershell.core/about/about_profiles).

В функциях PowerShell скрипт профиля выполняется при запуске приложения функции. Приложения-функции запускаются при первом развертывании и после бездействия ([холодный запуск](#cold-start)).

При создании приложения-функции с помощью средств, таких как Visual Studio Code и Azure functions Core Tools, создается значение `profile.ps1` по умолчанию. Профиль по умолчанию хранится [в репозитории основных средств](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) , который содержит:

* Автоматическая проверка подлинности MSI в Azure.
* Возможность включения Azure PowerShell `AzureRM` псевдонимов PowerShell при желании.

## <a name="powershell-version"></a>Версия PowerShell

В следующей таблице показана версия PowerShell, используемая каждой основной версией среды выполнения функций.

| Версия службы "Функции" | Версия PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5,1 (заблокировано средой выполнения) |
| 2.x               | PowerShell Core 6                              |

Текущую версию можно просмотреть, выполнив печать `$PSVersionTable` из любой функции.

## <a name="dependency-management"></a>Управление зависимостями

Функции PowerShell поддерживают скачивание модулей [коллекции PowerShell](https://www.powershellgallery.com) и управление ими с помощью службы. Изменяя Host. JSON и устанавливая для свойства Манажеддепенденци Enabled значение true, файл требований. PSD1 будет обработан. Указанные модули будут автоматически скачаны и станут доступными для функции. 

Максимальное число модулей, поддерживаемое в настоящее время, равно 10. Поддерживаемый синтаксис — Мажорнумбер. * или точная версия модуля, как показано ниже. Модуль Azure AZ включается по умолчанию при создании нового приложения-функции PowerShell.

Языковой рабочий процесс будет получать обновленные модули при перезапуске.

Host. JSON
```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

требования. PSD1

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Использование собственных пользовательских модулей немного отличается от того, как это можно сделать обычным образом.

При установке модуля на локальном компьютере он помещается в одну из глобально доступных папок в `$env:PSModulePath`. Так как ваша функция выполняется в Azure, вы не сможете получить доступ к модулям, установленным на вашем компьютере. `$env:PSModulePath` Для этого требуется, чтобы приложение-функция PowerShell отличалось `$env:PSModulePath` от в регулярном скрипте PowerShell.

В функциях `PSModulePath` содержит два пути:

* `Modules` Папка, которая находится в корне приложение-функция.
* Путь к `Modules` папке, которая находится внутри рабочей роли языка PowerShell.

### <a name="function-app-level-modules-folder"></a>Папка уровня `Modules` приложения-функции

Чтобы использовать пользовательские модули, можно разместить модули, от которых зависят функции, в `Modules` папке. Из этой папки модули автоматически становятся доступными для среды выполнения функций. Все функции в приложении функции могут использовать эти модули. 

> [!NOTE]
> Модули, указанные в файле требований. PSD1, автоматически загружаются и включаются в путь, поэтому их не нужно включать в папку Modules. Они хранятся локально в папке $env: LOCALAPPDATA/Азурефунктионс и в папке/Дата/манажеддепенденЦиес при запуске в облаке.

Чтобы воспользоваться функцией пользовательского модуля, создайте `Modules` папку в корне приложения-функции. Скопируйте модули, которые вы хотите использовать в функциях, в это расположение.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

В папке modules приложение-функция должно иметь следующую структуру папок:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

При запуске приложения-функции языковой рабочий процесс PowerShell добавляет эту `Modules` папку `$env:PSModulePath` в, чтобы вы могли полагаться на автозагрузку модуля точно так же, как в обычном сценарии PowerShell.

### <a name="language-worker-level-modules-folder"></a>Папка уровня `Modules` рабочей роли языка

Рабочие роли языка PowerShell обычно используют несколько модулей. Эти модули определяются в последней должности `PSModulePath`. 

Текущий список модулей выглядит следующим образом:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): модуль, используемый для работы с архивами `.zip`, `.nupkg`например, и другими.
* **Среаджоб**: Реализация интерфейсов API задания PowerShell на основе потока.

Самые последние версии этих модулей используются функциями. Чтобы использовать определенную версию этих модулей, можно разместить определенную версию в `Modules` папке приложения-функции.

## <a name="environment-variables"></a>Переменные среды

В Функциях [параметры приложения](functions-app-settings.md), такие как строки подключения службы, доступны в виде переменных среды во время выполнения. Доступ к этим параметрам можно `$env:NAME_OF_ENV_VAR`получить с помощью, как показано в следующем примере:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

При локальном запуске приложения параметры считываются из файла проекта [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="concurrency"></a>Параллелизм

По умолчанию среда выполнения PowerShell для функций может обрабатывать только один вызов функции за раз. Однако этот уровень параллелизма может быть недостаточно в следующих ситуациях:

* При одновременной обработке большого количества вызовов одновременно.
* При наличии функций, которые вызывают другие функции в одном и том же приложении функции.

Это поведение можно изменить, задав для следующей переменной среды целочисленное значение:

```
PSWorkerInProcConcurrencyUpperBound
```

Эта переменная среды задается в [параметрах приложения](functions-app-settings.md) приложение-функция.

### <a name="considerations-for-using-concurrency"></a>Рекомендации по использованию параллелизма

По умолчанию PowerShell является _отдельным потоковым_ языком сценариев. Однако параллелизм можно добавить с помощью нескольких пространств выполнения PowerShell в одном процессе. Объем созданных пространств выполнения будет соответствовать параметру приложения Псворкеринпрокконкурренциуппербаунд. На пропускную способность влияет объем ресурсов ЦП и памяти, доступный в выбранном плане.

Azure PowerShell использует некоторые контексты и состояния _уровня процесса_ , чтобы помочь сэкономить от чрезмерного ввода. Однако при включении параллелизма в приложении функции и вызове действий, изменяющих состояние, могут возникнуть состояния гонки. Эти состояния гонки трудно отлаживать, поскольку один вызов зависит от определенного состояния, а другой вызов изменил состояние.

Во время параллелизма Azure PowerShell, так как некоторые операции могут занимать значительное количество времени. Однако следует соблюдать осторожность. Если вы считаете, что столкнулись с состоянием гонки, задайте для `1` параметра приложения псворкеринпрокконкурренциуппербаунд значение и вместо этого используйте [изоляцию уровня языкового рабочего процесса](functions-app-settings.md#functions_worker_process_count) для параллелизма.

## <a name="configure-function-scriptfile"></a>Настройка функции`scriptFile`

По умолчанию функция PowerShell выполняется из `run.ps1`файла, который использует тот же родительский каталог, что и соответствующий. `function.json`

`scriptFile` Свойство`function.json` в можно использовать для получения структуры папок, которая выглядит, как в следующем примере:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

В этом случае `function.json` для `myFunction` включает `scriptFile` свойство, ссылающееся на файл с экспортированной функцией для выполнения.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Использование модулей PowerShell с помощью настройки точки входа

В этой статье показаны функции PowerShell в файле сценария `run.ps1` по умолчанию, созданном шаблонами.
Однако можно также включить функции в модули PowerShell. Вы можете ссылаться на код конкретной функции в модуле, используя `scriptFile` поля и `entryPoint` в файле конфигурации Function. JSON.

В этом случае `entryPoint` — это имя функции или командлета в модуле PowerShell, `scriptFile`на который ссылается.

Рассмотрим следующую структуру папок:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Где `PSFunction.psm1` содержит:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

В этом примере конфигурация `myFunction` `scriptFile` включает свойство, которое ссылается `PSFunction.psm1`на модуль PowerShell в другой папке.  Свойство ссылается на `Invoke-PSTestFunc` функцию, которая является точкой входа в модуле. `entryPoint`

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

В этой конфигурации объект `Invoke-PSTestFunc` выполняется точно `run.ps1` так же, как и.

## <a name="considerations-for-powershell-functions"></a>Рекомендации по функциям PowerShell

При работе с функциями PowerShell учитывайте рекомендации в следующих разделах.

### <a name="cold-start"></a>Холодный запуск

При разработке функций Azure в [модели размещения, не поддерживающей сервер](functions-scale.md#consumption-plan), холодный запуск — это реальность. *Холодный запуск* — это период времени, который требуется для запуска приложения-функции для обработки запроса. Холодный запуск чаще всего происходит в плане потребления, так как приложение-функция завершает работу в периоды бездействия.

### <a name="bundle-modules-instead-of-using-install-module"></a>Используйте модули пакета вместо`Install-Module`

Сценарий выполняется при каждом вызове. Избегайте использования `Install-Module` в скрипте. Вместо этого `Save-Module` используйте перед публикацией, чтобы функция не могла тратить время на загрузку модуля. Если холодный запуск влияет на ваши функции, рассмотрите возможность развертывания приложения-функции в [плане службы приложений](functions-scale.md#app-service-plan) со значением *Always on* или в [плане Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительных сведений см. следующие ресурсы:

* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)

[Справочник по Host. JSON]: functions-host-json.md
