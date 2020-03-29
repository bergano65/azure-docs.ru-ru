---
title: Функции Debug PowerShell Azure локально
description: Понять, как разрабатывать функции с помощью PowerShell.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163766"
---
# <a name="debug-powershell-azure-functions-locally"></a>Функции Debug PowerShell Azure локально

Функции Azure позволяют разрабатывать функции скриптов PowerShell.

Вы можете отладить функции PowerShell локально, как и любые скрипты PowerShell, используя следующие стандартные инструменты разработки:

* [Визуальный код студии](https://code.visualstudio.com/): бесплатный, легкий и с открытым исходным кодом текстовый редактор Microsoft с расширением PowerShell, который предлагает полный опыт разработки PowerShell.
* Консоль PowerShell: отладка с использованием тех же команд, которые вы использовали бы для отладки любого другого процесса PowerShell.

[Основные инструменты Azure Functions](functions-run-local.md) поддерживают локальную отладку функций Azure, включая функции PowerShell.

## <a name="example-function-app"></a>Пример функции приложения

Функция приложение, используемое в этой статье имеет одну функцию http срабатывает и имеет следующие файлы:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Эта функция приложение похоже на тот, который вы получаете, когда вы завершаете [PowerShell quickstart](functions-create-first-function-powershell.md).

Функциональный код `run.ps1` в выглядит следующим скриптом:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Установка точки крепления

Чтобы отладить любую функцию PowerShell, функция должна остановиться для отладчика, который будет прикреплен. Cmdlet `Wait-Debugger` останавливает выполнение и ждет отладчика.

Все, что вам нужно сделать, это добавить вызов cmdlet `Wait-Debugger` чуть выше `if` оператора, а именно:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Отладка начинается `if` с оператора. 

Теперь, когда `Wait-Debugger` вы можете отладить функции, используя либо Visual Studio Code, либо консоль PowerShell.

## <a name="debug-in-visual-studio-code"></a>Ошибка в коде визуальной студии

Чтобы отладить функции PowerShell в Коде Visual Studio, необходимо установить следующие:

* [Расширение PowerShell для визуального кода студии](/powershell/scripting/components/vscode/using-vscode)
* [Расширение "Функции Azure" для Visual Studio Code](functions-create-first-function-vs-code.md)
* [Ядро PowerShell 6.2 или выше](/powershell/scripting/install/installing-powershell-core-on-windows)

После установки этих зависимостей загрузите существующий проект PowerShell Functions или [создайте свой первый проект PowerShell Functions.](functions-create-first-function-powershell.md)

>[!NOTE]
> Если ваш проект не имеет необходимых файлов конфигурации, вам предлагается добавить их.

### <a name="set-the-powershell-version"></a>Установка версии PowerShell

PowerShell Core устанавливается бок о бок с Windows PowerShell. Установите PowerShell Core в качестве версии PowerShell для использования с расширением PowerShell для визуального кода studio.

1. Нажмите F1, чтобы отобразить `Session`командный поддон, а затем ищите .

1. Выберите **PowerShell: Показать меню сессии**.

1. Если **текущая сессия** не **PowerShell Core 6,** **выберите переключатель на: PowerShell Core 6.**

При открытии файла PowerShell вы видите версию, отображаемую зеленым цветом в правом нижнем углу окна. При выборе этого текста также отображается меню сеанса. Чтобы узнать больше, смотрите [Выбор версии PowerShell для использования с расширением.](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)

### <a name="start-the-function-app"></a>Запустите функциональное приложение

Проверить, `Wait-Debugger` что устанавливается в функции, где вы хотите прикрепить отладчика.  С `Wait-Debugger` добавлением вы можете отладить приложение функции с помощью Visual Studio Code.

Выберите панель **Debug,** а затем **прикрепите к функции PowerShell.**

![отладчик](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Вы также можете нажать клавишу F5, чтобы начать отладку.

Операция отладки запуска выполняет следующие задачи:

* Выполняется `func extensions install` в терминале для установки любых расширений Функций Azure, требуемых вашим приложением функции.
* Запускается `func host start` в терминале, чтобы запустить функциональное приложение в хосте Функции.
* Прикрепите отладку PowerShell к беговому пространству PowerShell в течение времени выполнения функций.

>[!NOTE]
> Для обеспечения правильного отладки кода Visual Studio необходимо обеспечить, чтобы PSWorkerInProCConcurrencyUpperBound был настроен на 1. Это значение по умолчанию.

При запуске приложения функции вам нужна отдельная консоль PowerShell для вызова функции срабатывания HTTP.

В этом случае клиентом является консоль PowerShell. Используется `Invoke-RestMethod` для запуска функции.

В консоли PowerShell запустите следующую команду:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Вы заметите, что ответ не возвращается немедленно. Это потому, что `Wait-Debugger` прикрепил отладчик и PowerShell исполнения перешли в режим перерыва, как только он мог. Это из-за [концепции BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), которая объясняется позже. После нажатия `continue` кнопки отладчик теперь ломается `Wait-Debugger`на линии сразу после.

На этом этапе отладчик прилагается, и вы можете выполнять все обычные операции отладчика. Для получения дополнительной информации об использовании отладчика в Visual Studio Code, [см.](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)

После того как вы продолжите и полностью выссылаете свой сценарий, вы заметите, что:

* Консоль PowerShell, `Invoke-RestMethod` которая сделала, вернула результат
* Интегрированная консоль PowerShell в коде визуальной студии ждет выполнения сценария

Позже, когда вы вызываете ту же функцию, отладчик `Wait-Debugger`в расширении PowerShell ломается сразу после .

## <a name="debugging-in-a-powershell-console"></a>Отладка в консоли PowerShell

>[!NOTE]
> В этом разделе предполагается, что вы [прочитали документы о основных инструментах Azure Functions](functions-run-local.md) и знаете, как использовать `func host start` команду для запуска приложения функции.

Откройте консоль `cd` в каталог приложения функции и запустите следующую команду:

```sh
func host start
```

С функцией приложение `Wait-Debugger` работает и на месте, вы можете прикрепить к процессу. Вам нужно еще две консоли PowerShell.

Одна из консолей выступает в качестве клиента. Из этого вы `Invoke-RestMethod` вызываете, чтобы вызвать функцию. Например, можно запустить следующую команду:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Вы заметите, что он не возвращает ответ, который `Wait-Debugger`является результатом . В runspace PowerShell теперь ожидается присоединение отладчика. Давайте привяжем это.

В другой консоли PowerShell запустите следующую команду:

```powershell
Get-PSHostProcessInfo
```

Это cmdlet возвращает таблицу, которая выглядит как следующий выход:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Обратите внимание `ProcessId` на элемент в таблице `ProcessName` с `dotnet`как . Этот процесс является вашим функциональным приложением.

Далее запустите следующий фрагмент:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

После запуска отладчик ломается и показывает что-то вроде следующего вывода:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

На этом этапе вы остановились на точке разрыва в [отладчике PowerShell.](/powershell/module/microsoft.powershell.core/about/about_debuggers) Отсюда вы можете делать все обычные операции отладить, перешагнуть, шаг в, продолжить, бросить курить, и другие. Чтобы увидеть полный набор команд отладки, `h` доступных в консоли, запустите или `?` команды.

Вы также можете установить точки `Set-PSBreakpoint` разрыва на этом уровне с cmdlet.

Как только вы продолжите и полностью вызываете свой сценарий, вы заметите, что:

* Консоль PowerShell, на `Invoke-RestMethod` которой вы выполнили, теперь вернула результат.
* Консоль PowerShell, в `Debug-Runspace` которой вы выполняетеся, ждет выполнения сценария.

Вы можете снова вызвать ту `Invoke-RestMethod` же функцию (например, использовать) `Wait-Debugger` и перерывы отладчика сразу после команды.

## <a name="considerations-for-debugging"></a>Рассмотрение вопроса об отладке

Имейте в виду следующие проблемы при отладке кода Функции.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`может привести к тому, что ваш отладчик сломается в неожиданном месте

Расширение PowerShell `Debug-Runspace`использует , который, в свою `BreakAll` очередь, опирается на функцию PowerShell. Эта функция говорит PowerShell остановиться на первой выполняемых команд. Такое поведение дает возможность устанавливать точки разрыва в отладочном пространстве запуска.

Время выполнения функций Azure запускает несколько команд, `run.ps1` прежде чем на самом деле высвобождение скрипта, так что вполне возможно, что отладчик в конечном итоге нарушение внутри `Microsoft.Azure.Functions.PowerShellWorker.psm1` или `Microsoft.Azure.Functions.PowerShellWorker.psd1`

Если этот разрыв произойдет, запустите `continue` или `c` команду, чтобы пропустить эту точку разрыва. Затем вы останавливаетесь на ожидаемой точке разрыва.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о разработке функций с помощью PowerShell, смотрите [руководство разработчика Azure Functions PowerShell.](functions-reference-powershell.md)
