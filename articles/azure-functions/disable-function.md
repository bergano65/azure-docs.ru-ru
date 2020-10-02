---
title: Способы отключения функций в решении "Функции Azure"
description: Сведения о том, как отключать и включать функции в решении "Функции Azure".
ms.topic: conceptual
ms.date: 04/08/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 4d93f728103aabdd1bd5557033a8bd36ffac2d42
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91661029"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Способы отключения функций в решении "Функции Azure"

В этой статье объясняется, как отключить функцию в решении "Функции Azure". *Отключить* функцию означает сделать так, чтобы среда выполнения игнорировала автоматический триггер, который определен для этой функции. Это позволяет запретить выполнение определенной функции без остановки всего приложения-функции.

Рекомендуемым способом отключения функции является параметр приложения в формате `AzureWebJobs.<FUNCTION_NAME>.Disabled` `true` . Вы можете создавать и изменять такой параметр приложения разными способами, в том числе через [Azure CLI](/cli/azure/) или на вкладке **Управление** на [портале Azure](https://portal.azure.com). 

> [!NOTE]  
> Если вы отключите функцию, активируемую по HTTP, с помощью описанных в этой статье способов, ее конечная точка может остаться доступной, если она работает на локальном компьютере.  

## <a name="use-the-azure-cli"></a>Использование Azure CLI

В Azure CLI команда [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) позволяет создать или изменить параметр приложения. Следующая команда отключает функцию с именем `QueueTrigger`, создавая параметр приложения с именем `AzureWebJobs.QueueTrigger.Disabled` и значением `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Чтобы снова включить эту функцию, повторите ту же команду со значением `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Использование портала

Также можно использовать кнопки **Включить** и **Отключить** на странице **Обзор** для функции. Эти кнопки работают, изменяя значение `AzureWebJobs.<FUNCTION_NAME>.Disabled` параметра приложения. Этот параметр, зависящий от функции, создается при первом отключении.

![Переключатель состояния функции](media/disable-function/function-state-switch.png)

> [!NOTE]  
> Компоненты тестирования, интегрированные с порталом, игнорируют параметр `Disabled`. Это означает, что отключенная функция будет работать обычным образом, когда она запускается из окна **Тест** на портале. 

## <a name="localsettingsjson"></a>local.settings.json

Функции можно отключить тем же способом при локальном запуске. Чтобы отключить функцию с именем `HttpExample` , добавьте запись в коллекцию Values в local.settings.jsв файле, как показано ниже.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>Другие методы

Для всех языков и любых версий среды выполнения мы рекомендуем применять параметр приложения, но существуют и другие способы для отключения функций. Эти способы (зависят от языков и версий среды выполнения) поддерживаются только для обеспечения обратной совместимости. 

### <a name="c-class-libraries"></a>Библиотеки классов C#.

Для функции библиотеки классов вы также можете запретить активацию с помощью атрибута `Disable`. Можно использовать атрибут без параметра конструктора, как показано в следующем примере:

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

В версии 1.x можно использовать свойство `disabled` файла *function.json*, чтобы запретить среде выполнения активировать функцию. Этот метод работает только для скриптовых языков, например в скриптах на C# и JavaScript. Свойству `disabled` можно задать значение `true` или имя параметра приложения:

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

>[!IMPORTANT]  
>Теперь на портале для отключения функций версии 1.x используются параметры приложения. Если параметр приложения конфликтует с файлом function.json, может возникнуть ошибка. Чтобы избежать ошибок, следует удалить свойство `disabled` из файла function.json. 


## <a name="next-steps"></a>Дальнейшие действия

Эта статья посвящена отключению автоматических триггеров. Дополнительные сведения о триггерах см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).
