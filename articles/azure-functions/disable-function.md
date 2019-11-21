---
title: Способы отключения функций в решении "Функции Azure"
description: Узнайте, как отключать и включать функции в решении "Функции Azure" версии 1.x и 2.x.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7968580fcaa40575571a41f067fa74fbdc0a3a34
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233040"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Способы отключения функций в решении "Функции Azure"

В этой статье объясняется, как отключить функцию в решении "Функции Azure". *Отключить* функцию означает сделать так, чтобы среда выполнения игнорировала автоматический триггер, который определен для функции. Способ, с помощью которого вы это сделаете, зависит от версии среды выполнения и языка:

* Functions 2.x:
  * Один способ для всех языков.
  * Дополнительный способ для библиотек классов C#.
* Functions 1.x:
  * Языки сценариев.
  * Библиотеки классов C#.

## <a name="functions-2x---all-languages"></a>Функции 2.x — все языки

In Functions 2.x, you disable a function by using an app setting in the format `AzureWebJobs.<FUNCTION_NAME>.Disabled`. You can create and modify this application setting in a number of ways, including by using the [Azure CLI](/cli/azure/) and from your function's **Manage** tab in the [Azure portal](https://portal.azure.com). 

### <a name="azure-cli"></a>Azure CLI

In the Azure CLI, you use the [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command to create and modify the app setting. The following command disables a function named `QueueTrigger` by creating an app setting named `AzureWebJobs.QueueTrigger.Disabled` set it to `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

To re-enable the function, rerun the same command with a value of `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Microsoft Azure

You can also use the **Function State** switch on the function's **Manage** tab. The switch works by creating and deleting the `AzureWebJobs.<FUNCTION_NAME>.Disabled` app setting.

![Переключатель состояния функции](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Функции 2.x — библиотеки классов C#

В библиотеке классов Функций 2.x рекомендуется использовать метод, который работает для всех языков. Но при желании вы можете [использовать атрибут Disable, как в Функциях 1.x](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Функции 1.x — языки сценариев

Для языков сценариев, таких как сценарий C# и JavaScript, используйте свойство `disabled` файла *function.json*, чтобы сообщить среде выполнения не активировать функцию. Этому свойству можно задать значение `true` или имя параметра приложения:

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

You can edit the file in the Azure portal or use the **Function State** switch on the function's **Manage** tab. The portal switch works by changing the *function.json* file.

![Переключатель состояния функции](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Функции 1.x — библиотеки классов C#

В библиотеке классов Функций 1.x используйте атрибут `Disable` для предотвращения активации функции. Можно использовать атрибут без параметра конструктора, как показано в следующем примере:

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

## <a name="next-steps"></a>Дальнейшие действия

Эта статья посвящена отключению автоматических триггеров. Дополнительные сведения о триггерах см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).
