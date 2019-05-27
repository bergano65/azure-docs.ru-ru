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
ms.author: tyleonha, glenga
ms.openlocfilehash: 46b1e5c99dd86fed6f87ac3b8f0ff6555187899b
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833521"
---
# <a name="azure-functions-powershell-developer-guide"></a>Руководство разработчика Azure PowerShell функции.

Эта статья содержит сведения о способах написания функций Azure с помощью PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Функции PowerShell Azure (function) представляются в виде сценария PowerShell, который выполняется при активации. Каждый сценарий функция имеет связанный с ним `function.json` файл, который определяет, как функция ведет себя, например, как оно активируется и его входных и выходных параметров. Дополнительные сведения см. в разделе [триггеры и привязки статье](functions-triggers-bindings.md). 

Как и другие виды функций, функции сценария PowerShell имеют аргументов, совпадающие с именами входных привязок, определенных в `function.json` файл. Объект `TriggerMetadata` параметр также передается с дополнительными сведениями по триггеру, который работы функции.

В этой статье предполагается, что вы уже прочли [руководство для разработчиков по Функциям Azure](functions-reference.md). Необходимо также завершить [быстрый запуск функций для PowerShell](functions-create-first-function-powershell.md) создать свою первую функцию PowerShell.

## <a name="folder-structure"></a>Структура папок

Структура папок, необходимых для проекта PowerShell выглядит следующим образом. Это значение по умолчанию можно изменить. Дополнительные сведения см. в разделе о [scriptFile](#configure-function-scriptfile) ниже.

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

В корневой папке проекта, не существует общего [ `host.json` ](functions-host-json.md) файл, который может использоваться для настройки приложения-функции. У каждой функции есть папка с свой собственный файл кода (.ps1) и файл конфигурации привязки (`function.json`). Имя каталога родительского файла function.json всегда является именем этой функции.

Некоторые привязки требуется наличие `extensions.csproj` файл. Привязка расширения, необходимые в [версии 2.x](functions-versions.md) среды выполнения функций, определенных в `extensions.csproj` файл с файлами фактическое библиотеки в `bin` папку. При локальной разработке необходимо [зарегистрировать расширения привязки](functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles). При разработке функций на портале Azure эта регистрация выполняется автоматически.

В приложения-функции PowerShell, при необходимости может быть `profile.ps1` которой запускается при запуске приложения-функции (в противном случае известна под  *[холодный запуск](#cold-start)*. Дополнительные сведения см. в разделе [профиль PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Определение сценария PowerShell, как функция

По умолчанию среда выполнения Функций ищет функцию в файле `run.ps1`, где `run.ps1` использует тот же родительский каталог, что и соответствующий файл `function.json`.

Сценарий будет передано несколько аргументов в выполнения. Для обработки этих параметров необходимо добавить `param` блок в верхнюю часть сценария, как показано в следующем примере:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Параметр TriggerMetadata

`TriggerMetadata` Параметр используется для предоставления дополнительных сведений о триггере. Дополнительные метаданные зависит от привязки для привязки, но все они содержат `sys` свойство, которое содержит следующие данные:

```powershell
$TriggerMetadata.sys
```

| Свойство   | Description                                     | type     |
|------------|-------------------------------------------------|----------|
| utcNow     | Когда в формате UTC, время запуска функции        | DateTime |
| MethodName | Имя функции, которая была активирована     | string   |
| RandGuid   | Уникальный идентификатор guid для этого выполнения функции | string   |

Каждый тип триггера имеет другой набор метаданных. Например `$TriggerMetadata` для `QueueTrigger` содержит `InsertionTime`, `Id`, `DequeueCount`, помимо прочего. Дополнительные сведения о метаданных триггера очереди, см. в статье [официальной документации для триггеров очереди](functions-bindings-storage-queue.md#trigger---message-metadata). См. в документации на [триггеры](functions-triggers-bindings.md) вы работаете с чтобы посмотреть, что придет в метаданные триггера.

## <a name="bindings"></a>Привязки

В PowerShell [привязки](functions-triggers-bindings.md) настраиваются и определенные в function.json функции. Функции взаимодействуют с привязками несколькими способами.

### <a name="reading-trigger-and-input-data"></a>Чтение триггера и входных данных

Триггер и привязки ввода считываются в качестве параметров, переданных функции. Входные привязки имеют `direction` присвоено `in` в function.json. `name` Свойство, определенное в `function.json` — имя параметра, в `param` блока. Так как PowerShell использует именованные параметры для привязки, порядок параметров не имеет значения. Тем не менее, рекомендуется следовать порядок привязки, определенные в `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Запись выходных данных

В функциях, имеет выходной привязки `direction` присвоено `out` в function.json. Можно написать в выходную привязку с помощью `Push-OutputBinding` командлет, который доступен для выполнения функций. Во всех случаях `name` свойство привязки, как определено в `function.json` соответствует `Name` параметр `Push-OutputBinding` командлета.

Далее показано, как вызывать `Push-OutputBinding` в скрипте функции:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Можно также передать значение для указанной привязки по конвейеру.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` ведет себя по-разному в зависимости от значений, указанных для `-Name`:

* Если указанное имя не может разрешаться в допустимый выходной привязки, создается исключение.

* Если выходная привязка принимает коллекцию значений, можно вызвать `Push-OutputBinding` несколько раз, чтобы отправить несколько значений.

* Если выходная привязка принимает только одноэлементное значение, вызов `Push-OutputBinding` во второй раз вызывает ошибку.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` Синтаксис

Ниже приведены допустимые параметры для вызова метода `Push-OutputBinding`:

| ИМЯ | type | Положение | Описание |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | Имя привязки для вывода требуется задать. |
| **`-Value`** | Object | 2 | Значение привязки для вывода вы хотите задать, который принимается из канала ByValue. |
| **`-Clobber`** | SwitchParameter | именованная | (Необязательно) Если указано, принудительно устанавливает значение для указанной выходной привязки. | 

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

Дополнительные сведения см. в разделе [о CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Пример OutputBinding Push-уведомлений. Ответы HTTP

Триггер HTTP возвращает ответ, используя выходную привязку с именем `response`. В следующем примере выходных данных привязку `response` имеет значение «выход #1»:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Поскольку выходные данные НТТР, который принимает только одноэлементное значение, ошибка возникает, когда `Push-OutputBinding` вызывается второй раз.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Для выходных данных, которые только принимают одноэлементных значений, можно использовать `-Clobber` параметр, чтобы переопределить старое значение вместо того, чтобы добавить в коллекцию. В следующем примере предполагается, что вы уже добавили значение. С помощью `-Clobber`, существующее значение для возврата значения выходных данных «#3» переопределяет ответ из следующего примера:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Пример OutputBinding Push-уведомлений. Выходная привязка очереди

`Push-OutputBinding` используется для отправки данных в выходные привязки, такие как [выходная привязка хранилища очередей Azure](functions-bindings-storage-queue.md#output). В следующем примере сообщения, записанного в очередь имеет значение «выход #1»:

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Выходная привязка для очереди хранилища принимает несколько значений выходных данных. В этом случае вызов в следующем примере после первая запись в очередь списка с двумя элементами: «выходные данные #1» и «#2 ".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

В следующем примере, при вызове после два предыдущих добавляется еще две характеристики значений в коллекцию выходных данных:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

При записи в очередь, сообщение содержит эти четыре значения: «Выход #1», «выход #2», «выход #3» и «выход #4».

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` командлет

Можно использовать `Get-OutputBinding` командлет для получения значений, заданных в настоящее время для привязок выходных данных. Этот командлет извлекает хэш-таблицы, содержащий имена выходных привязок с помощью соответствующих значений. 

Ниже приведен пример использования `Get-OutputBinding` для возврата текущего значения привязки:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` также содержит параметр с именем `-Name`, который может использоваться для фильтрации возвращаемых привязки, как показано в следующем примере:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Поддерживаются подстановочные знаки (*), в `Get-OutputBinding`.

## <a name="logging"></a>Ведение журналов

Ведение журнала в функции PowerShell работает подобно регулярное ведение журнала PowerShell. Можно использовать командлеты ведения журнала для записи каждой выходной поток. Каждый командлет сопоставляет уровень ведения журнала, используемые функциями.

| Уровень ведения журнала функций | Командлет ведения журнала |
| ------------- | -------------- |
| Ошибка | **`Write-Error`** |
| Предупреждение | **`Write-Warning`**  | 
| Информация | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Информация | Записывает _сведения_ уровня ведения журнала. |
| Отладка | **`Write-Debug`** |
| Трассировка | **`Write-Progress`** <br /> **`Write-Verbose`** |

Помимо этих командлетов, все записи в конвейер, перенаправляется на `Information` журнала уровня и отображается с форматированием по умолчанию PowerShell.

> [!IMPORTANT]
> С помощью `Write-Verbose` или `Write-Debug` командлетов не является достаточным для см. в разделе verbose и уровня ведение журнала отладки. Необходимо также настроить журнал пороговое значение уровня, который объявляет уровень журналы, вас волнует. Дополнительные сведения см. в разделе [настроить уровень ведения журнала приложения функции](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Настройка уровне журнала приложения-функции

Функции можно определить пороговое значение уровня для упрощения управления способом функции записывает в журналы. Чтобы задать пороговое значение для всех трассировок, которые записываются в консоль, используйте `logging.logLevel.default` свойство в [ `host.json` файл][справочник по файлу host.json]. Этот параметр применяется ко всем функциям в приложении-функции.

В следующем примере Задает пороговое значение, чтобы включить подробное ведение журнала для всех функций, но Задает пороговое значение, чтобы включить ведение журнала отладки для функции с именем `MyFunction`:

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

Дополнительные сведения см. в [Справочник по файлу Host.JSON].

### <a name="viewing-the-logs"></a>Просмотр журналов

Если ваше приложение-функция выполняется в Azure, Application Insights можно использовать для него отслеживания программой. Дополнительные сведения о просмотре журналов функций и обращении к ним см. в статье [мониторинг Функций Azure](functions-monitoring.md).

Если вы используете приложения-функции локально для разработки приложений, журналы по умолчанию в файловой системе. Чтобы просмотреть журналы в консоль, задайте `AZURE_FUNCTIONS_ENVIRONMENT` переменную среды, чтобы `Development` перед запуском приложения-функции.

## <a name="triggers-and-bindings-types"></a>Триггеры и привязки типов

Существует ряд триггеров и привязок, доступных для использования с помощью приложения-функции. Полный список триггеров и привязок [можно найти здесь](functions-triggers-bindings.md#supported-bindings).

Все триггеры и привязки в коде представлены как несколько типов реальные данные:

* Хэш-таблицы
* string
* byte[]
* int
* Double
* HttpRequestContext
* HttpResponseContext

Первые пять типов в этом списке являются стандартных типов .NET. Последние два используются только [HttpTrigger триггера](#http-triggers-and-bindings).

Каждый параметр привязки в функциях, должен быть один из этих типов.

### <a name="http-triggers-and-bindings"></a>Триггеры и привязки HTTP

Триггеры HTTP и webhook, а также привязки вывода HTTP используют объекты запроса и ответа для обмена сообщениями HTTP.

#### <a name="request-object"></a>Объект запроса

Объект запроса, который передается в сценарий относится к типу `HttpRequestContext`, который имеет следующие свойства:

| Свойство  | Description                                                    | type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Объект, содержащий текст запроса. `Body` сериализуется в наилучший тип на основе данных. Например если данные JSON, он передается как хэш-таблицы. Если данные строки, передается в виде строки. | object |
| **`Headers`** | Словарь, содержащий заголовки запроса.                | Словарь < string, строка ><sup>*</sup> |
| **`Method`** | Метод HTTP, используемый для запроса.                                | string                    |
| **`Params`**  | Объект, содержащий параметры маршрутизации запроса. | Словарь < string, строка ><sup>*</sup> |
| **`Query`** | Объект, содержащий параметры запроса.                  | Словарь < string, строка ><sup>*</sup> |
| **`Url`** | URL-адрес запроса.                                        | string                    |

<sup>*</sup> Все `Dictionary<string,string>` ключи не учитывают регистр.

#### <a name="response-object"></a>Объект ответа

Объект ответа, который следует отправлять обратно относится к типу `HttpResponseContext`, который имеет следующие свойства:

| Свойство      | Description                                                 | type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Объект, содержащий текст ответа.           | object                    |
| **`ContentType`** | Короткий руки для задав тип содержимого для ответа. | string                    |
| **`Headers`** | Объект, содержащий заголовок ответа.               | Словаря или хэш-таблицы   |
| **`StatusCode`**  | Код состояния HTTP ответа.                       | строка или целое число             |

#### <a name="accessing-the-request-and-response"></a>Доступ к запросу и ответу

При работе с триггерами HTTP, HTTP-запроса можно получить так же, как с помощью входной привязки. Он находится в `param` блока.

Используйте `HttpResponseContext` объект вернуть ответ, как показано в следующем:

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

В результате вызова этой функции будет следующим:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Приведение типов для триггеров и привязок

Для определенных привязок, такие как привязка большого двоичного объекта, вы можете указать тип параметра.

Например, чтобы данные из хранилища BLOB-объектов, предоставленные в виде строки, добавьте следующий тип приведен к моей `param` блок:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Профиль PowerShell

В PowerShell используется понятие профиль PowerShell. Если вы не знакомы с PowerShell профили, см. в разделе [о профилях](/powershell/module/microsoft.powershell.core/about/about_profiles).

В функции PowerShell сценарий профиля выполняется при запуске приложения-функции. Запуск приложения-функции, при первом развертывании и после приостановлены ([холодный запуск](#cold-start)).

При создании приложения-функции с помощью средств, таких как Visual Studio Code и основных инструментов функций Azure, по умолчанию `profile.ps1` создается автоматически. Профиль по умолчанию сохраняется [в репозитории GitHub средства Core](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) и содержит:

* Автоматическая аутентификация MSI в Azure.
* Возможность включения в Azure PowerShell `AzureRM` псевдонимов PowerShell, если вы хотите.

## <a name="powershell-version"></a>Версия PowerShell

В следующей таблице показаны версии PowerShell, используемой каждой основной версии среды выполнения функций:

| Версия службы "Функции" | Версия PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (заблокировано средой выполнения) |
| 2.x               | PowerShell Core 6                              |

См. текущую версию с печатью `$PSVersionTable` из любой функции.

## <a name="dependency-management"></a>Управление зависимостями

Функции PowerShell поддерживают управление службой модули Azure. Путем изменения host.json и поддержкой managedDependency свойству присвоено значение true, файл requirements.psd1 будет обработан. Последние модули Azure будет автоматически загружаются и становятся доступными в функцию.

Host.JSON
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

Используя собственные модули или модули из [коллекции PowerShell](https://powershellgallery.com) выглядит немного иначе, чем что бы вы делали его обычным образом.

При установке модуля на локальном компьютере, он переходит в одном из глобально доступной папки в вашей `$env:PSModulePath`. Так как функция выполняется в Azure, будут недоступны в модулях, установленных на компьютере. Это необходимо, `$env:PSModulePath` для функции PowerShell приложение отличается от `$env:PSModulePath` в обычный сценарий PowerShell.

В функциях `PSModulePath` содержит два пути:

* Объект `Modules` папки, которая существует в корне приложения-функции.
* Путь к `Modules` папку, находящуюся внутри рабочего языка PowerShell.

### <a name="function-app-level-modules-folder"></a>Приложения на уровне функций `Modules` папки

Чтобы использовать пользовательские модули или модули PowerShell из коллекции PowerShell, можно поместить модули, от которых зависят ваши функции в `Modules` папку. Из этой папки модули автоматически доступны для выполнения функций. Эти модули можно использовать любой функции в приложении-функции.

Чтобы воспользоваться преимуществами этой функции, создайте `Modules` папка в корне приложения-функции. Сохраните модули, которые вы хотите использовать в функциях в этом расположении.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

Используйте `Save-Module` чтобы сохранить все модули, используйте функции, или скопируйте собственных настраиваемых модулей для `Modules` папки. В папке модулей приложения-функции должен иметь следующую структуру папок:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

При запуске приложения-функции, рабочая роль языка PowerShell добавляет это `Modules` папку `$env:PSModulePath` таким образом, можно использовать модуль Автозагрузка так же, как в обычный сценарий PowerShell.

### <a name="language-worker-level-modules-folder"></a>Уровень рабочей роли языка `Modules` папки

Несколько модулей часто используемых рабочих языка PowerShell. Эти модули определены в последней позиции `PSModulePath`. 

Текущий список модулей выглядит следующим образом:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): модуль, используемый для работы с архивами, например `.zip`, `.nupkg`и другие.
* **ThreadJob**: Реализация потокового задания PowerShell API-интерфейсы.

Самую последнюю версию модулей используется функциями. Чтобы использовать конкретную версию эти модули, можно поместить определенной версии в `Modules` папке приложения-функции.

## <a name="environment-variables"></a>Переменные среды

В Функциях [параметры приложения](functions-app-settings.md), такие как строки подключения службы, доступны в виде переменных среды во время выполнения. Вы можете получить доступ к эти параметры с помощью `$env:NAME_OF_ENV_VAR`, как показано в следующем примере:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

При локальном запуске приложения параметры считываются из файла проекта [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="concurrency"></a>Параллелизм

По умолчанию среда выполнения функций PowerShell может обрабатывать только один вызов функции одновременно. Тем не менее этот уровень параллелизма может оказаться недостаточно в следующих ситуациях:

* Когда вы пытаетесь обрабатывать большое количество вызовов, в то же время.
* При наличии функции, которые вызывают другие функции в одном приложении-функции.

Это поведение можно изменить, задав следующую переменную среды в целочисленное значение:

```
PSWorkerInProcConcurrencyUpperBound
```

Задать переменную среды [параметры приложения](functions-app-settings.md) приложения-функции.

### <a name="considerations-for-using-concurrency"></a>Рекомендации по использованию параллелизма

PowerShell — _однопоточная_ язык сценариев по умолчанию. Тем не менее параллелизма можно добавить с помощью нескольких пространства выполнения PowerShell в одном процессе. Эта функция доступна, как работает среда выполнения функций Azure PowerShell.

Есть некоторые недостатки этого подхода.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>Параллелизма только от компьютера, на котором он работает под управлением

Если ваше приложение-функция работает под управлением [план службы приложений](functions-scale.md#app-service-plan) , поддерживает только одно ядро, а затем параллелизма практически не поможет. Это, так как нет дополнительных ядер для балансировки нагрузки. В этом случае производительность может меняться от одного ядра, которым приходится переключения контекста между пространства выполнения.

[План потребления](functions-scale.md#consumption-plan) запускается с использованием только одно ядро, поэтому не может использовать параллелизм. Если вы хотите воспользоваться всеми преимуществами параллелизма, вместо развертывания своих функций к приложению-функции на выделенном плане службы приложений с недостаточно ядер.

#### <a name="azure-powershell-state"></a>Состояние Azure PowerShell

Azure PowerShell использует некоторые _уровня процесса_ контексты и состояние для экономии лишние вводят. Тем не менее если вы включите параллелизма в приложении-функции и выполнять действия, которые изменяют состояние, вы может получиться гонки. Эти условия конкуренции, сложно отладку, так как один вызов зависит от определенного состояния и других вызовов изменено состояние.

Имеется огромное значение при параллелизме с помощью Azure PowerShell, так как некоторые операции может занять значительное время. Тем не менее вы должны перейти с осторожностью. Если вы подозреваете, что у вас возникли состязания, параллелизм набора к `1` и повторите запрос.

## <a name="configure-function-scriptfile"></a>Настройка функции `scriptFile`

По умолчанию функция PowerShell выполняется из `run.ps1`, файл, который совместно использует один родительский каталог с соответствующим ему аргументом `function.json`.

`scriptFile` Свойство в `function.json` может использоваться для получения структуру папок, которая выглядит как в следующем примере:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

В этом случае `function.json` для `myFunction` включает в себя `scriptFile` свойство ссылается на файл с экспортированную функцию для запуска.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Использовать модули PowerShell, настроив entryPoint

В этой статье показано функций PowerShell в используемом по умолчанию `run.ps1` файл скрипта, создаваемым шаблонами.
Тем не менее можно также включить вашей функции в модулях PowerShell. Может ссылаться на определенную функцию кода в модуле с помощью `scriptFile` и `entryPoint` полей в function.json "файла конфигурации.

В этом случае `entryPoint` — это имя функции или командлета в модуле PowerShell, на которые ссылается `scriptFile`.

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

В этом примере конфигурации для `myFunction` включает в себя `scriptFile` свойство, ссылающееся на `PSFunction.psm1`, который представляет собой модуль PowerShell в другой папке.  `entryPoint` Ссылок на свойства `Invoke-PSTestFunc` функцию, которая представляет собой точку входа в модуле.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

В такой конфигурации `Invoke-PSTestFunc` выполняется точно так, как `run.ps1` бы.

## <a name="considerations-for-powershell-functions"></a>Рекомендации для функций PowerShell

При работе с функциями PowerShell, следует учитывать в следующих разделах.

### <a name="cold-start"></a>Холодный запуск

При разработке функций Azure в [бессерверных модель размещения](functions-scale.md#consumption-plan), холодного запуска — это реальностью. *Холодный запуск* ссылается на период времени он занимает в свое приложение-функцию запуска для обработки запроса. Холодный запуск происходит чаще, потребления планирование, так как ваше приложение-функция возвращает завершить работу во время простоя.

### <a name="bundle-modules-instead-of-using-install-module"></a>Модули пакета вместо использования `Install-Module`

Ваш сценарий будет выполнен при каждом вызове. Избегайте использования `Install-Module` в скрипте. Вместо этого используйте `Save-Module` перед публикацией, функции не придется тратить время на загрузке модуля. При холодном запуске влияют на ваши функции, рассмотрите возможность развертывания приложения-функции для [план службы приложений](functions-scale.md#app-service-plan) присвоено *всегда включено* или [план "премиум"](functions-scale.md#premium-plan-public-preview).

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)

[Справочник по файлу Host.JSON]: functions-host-json.md
