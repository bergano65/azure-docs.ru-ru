---
title: Способы отключения функций в решении "Функции Azure"
description: Узнайте, как отключить и включить функции в функциях Azure.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: bffb3136c77074ecd50e839fd7c73144ad910967
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970981"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Способы отключения функций в решении "Функции Azure"

В этой статье объясняется, как отключить функцию в решении "Функции Azure". *Отключить* функцию означает сделать так, чтобы среда выполнения игнорировала автоматический триггер, который определен для функции. Это позволяет предотвратить выполнение определенной функции без остановки всего приложения-функции.

Рекомендуемый способ отключения функции — использовать параметр приложения в формате `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Вы можете создать и изменить этот параметр приложения несколькими способами, в том числе с помощью [Azure CLI](/cli/azure/) и вкладки " **Управление** " функции в [портал Azure](https://portal.azure.com). 

## <a name="use-the-azure-cli"></a>Использование Azure CLI

В Azure CLI используется команда [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) для создания и изменения параметра приложения. Следующая команда отключает функцию с именем `QueueTrigger`, создавая параметр приложения с именем `AzureWebJobs.QueueTrigger.Disabled` присвоив ему значение `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Чтобы повторно включить функцию, повторно запустите ту же команду со значением `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Использование портала

Можно также использовать параметр **состояния функции** на вкладке **Управление** функции. Этот параметр работает путем создания и удаления параметра `AzureWebJobs.<FUNCTION_NAME>.Disabled` приложения.

![Переключатель состояния функции](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>Другие методы

Хотя метод установки приложения рекомендуется для всех языков и всех версий среды выполнения, существует несколько других способов отключения функций. Эти методы, зависящие от языка и версии среды выполнения, поддерживаются для обеспечения обратной совместимости. 

### <a name="c-class-libraries"></a>Библиотеки классов C#.

В функции библиотеки классов можно также использовать атрибут `Disable`, чтобы предотвратить срабатывание функции. Можно использовать атрибут без параметра конструктора, как показано в следующем примере:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Если используется атрибут без параметра конструктора, необходимо перекомпилировать и повторно развернуть проект, чтобы изменить отключенное состояние функции. Более гибкий способ использования атрибута — включить параметр конструктора, который ссылается на логический параметр приложение, как показано в следующем примере:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Этот метод позволяет включать и отключать функцию, изменив параметр приложения без повторной компиляции или повторного развертывания. Изменение параметра приложения вызывает перезапуск приложения-функции, поэтому изменение отключенного состоянии сразу же распознается.

> [!IMPORTANT]
> Атрибут `Disabled` является единственным способом отключить функцию библиотеки классов. Созданный файл *function.json* для функции библиотеки классов не предназначен для непосредственного редактирования. Во время редактирования этого файла любые действия со свойством `disabled` не окажут никакого влияния.
>
> То же самое касается переключателя **состояния функции** на вкладке **Управление**, так как он работает, изменяя файл *function.json*.
>
> Обратите также внимание, что портал может указывать, что функция отключена, когда это не так.

### <a name="functions-1x---scripting-languages"></a>Функции 1.x — языки сценариев

В версии 1. x можно также использовать свойство `disabled` файла *Function. JSON* , чтобы указать среде выполнения не запускать функцию. Этот метод работает только для языков сценариев, таких как C# script и JavaScript. Свойству `disabled` может быть присвоено значение `true` или имя параметра приложения:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
или 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

Во втором примере функция отключается, если имеется параметр приложения с именем IS_DISABLED и ему задано значение `true` или 1.

Файл можно изменить в портал Azure или использовать параметр **состояния функции** на вкладке **Управление** функции. Переключение на портал выполняется путем изменения файла *Function. JSON* .

![Переключатель состояния функции](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>Дальнейшие действия

Эта статья посвящена отключению автоматических триггеров. Дополнительные сведения о триггерах см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).
