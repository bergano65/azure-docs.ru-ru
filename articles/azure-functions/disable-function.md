---
title: Способы отключения функций в решении "Функции Azure"
description: Узнайте, как отключить и включить функции в функциях Azure.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: fb8edf635856078655b8640ba0e1723fdd5e8a5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116141"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Способы отключения функций в решении "Функции Azure"

В этой статье объясняется, как отключить функцию в решении "Функции Azure". *Отключить* функцию означает сделать так, чтобы среда выполнения игнорировала автоматический триггер, который определен для функции. Это позволяет предотвратить работу определенной функции, не останавливая все приложение функции.

Рекомендуемый способ отключить функцию — это использование настройки `AzureWebJobs.<FUNCTION_NAME>.Disabled`приложения в формате. Вы можете создать и изменить эту настройку приложения несколькими способами, в том числе с помощью [Azure CLI](/cli/azure/) и со вкладки **«Управление»** на [портале Azure.](https://portal.azure.com) 

> [!NOTE]  
> При отбывающем функцию HTTP с помощью методов, описанных в этой статье, конечная точка может по-прежнему доступна при работе на локальном компьютере.  

## <a name="use-the-azure-cli"></a>Использование Azure CLI

В Azure CLI команда [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) используется для создания и изменения настройки приложения. Следующая команда отсутчает функцию, `QueueTrigger` `AzureWebJobs.QueueTrigger.Disabled` названную `true`путем создания настройки приложения, указанной в названии. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Чтобы повторно включить функцию, перезапустите одну `false`и ту же команду со значением .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Использование портала

Можно также использовать переключатель **состояния функций** на вкладке **«Управление** функцией». Переключатель работает путем создания `AzureWebJobs.<FUNCTION_NAME>.Disabled` и удаляния настройки приложения.

![Переключатель состояния функции](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>Другие методы

Хотя метод настройки приложения рекомендуется для всех языков и всех версий времени выполнения, существует несколько других способов отключить функции. Эти методы, которые различаются в зависимости от языка и версии времени выполнения, поддерживаются для обратной совместимости. 

### <a name="c-class-libraries"></a>Библиотеки классов C#.

В функции библиотеки класса можно `Disable` также использовать атрибут, чтобы предотвратить срабатывание функции. Можно использовать атрибут без параметра конструктора, как показано в следующем примере:

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

В версии 1.x можно `disabled` также использовать свойство файла *function.json,* чтобы сказать времени выполнения, чтобы не запускать функцию. Этот метод работает только для языков сценариев, таких как скрипт C и JavaScript. Свойство `disabled` может быть `true` установлено на или на имя настройки приложения:

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
или диспетчер конфигурации служб 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

Во втором примере функция отключается, если имеется параметр приложения с именем IS_DISABLED и ему задано значение `true` или 1.

Можно отсеять файл на портале Azure или использовать переключатель **состояния функций** в вкладке **«Управление** функцией». Переключатель портала работает путем изменения файла *function.json.*

![Переключатель состояния функции](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>Дальнейшие действия

Эта статья посвящена отключению автоматических триггеров. Дополнительные сведения о триггерах см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).
