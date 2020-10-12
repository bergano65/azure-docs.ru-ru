---
title: Выбор целевых версий среды выполнения Функций Azure
description: Решение "Функции Azure" поддерживает разные версии среды выполнения. Узнайте, как указать версию среды выполнения приложения-функции, размещенного в Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: a7d86ef26d50d60389ae09bf3245ed97fea2c3e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88926581"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Выбор целевых версий среды выполнения Функций Azure

Приложение-функция работает в определенной версии среды выполнения для решения "Функции Azure". Существует три основных версии: [1. x, 2. x и 3. x](functions-versions.md). По умолчанию приложения функций создаются в версии 3. x среды выполнения. Из этой статьи вы узнаете, как настроить приложение-функцию Azure для выполнения в выбранной версии. Сведения о настройке локальной среды разработки для определенной версии см. в разделе [Как программировать и тестировать функции Azure в локальной среде](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Обновление версий автоматически и вручную

Функции Azure позволяют ориентироваться на конкретную версию среды выполнения с помощью `FUNCTIONS_EXTENSION_VERSION` параметра приложения в приложении-функции. Приложение-функция будет работать в указанной основной версии, пока вы явно не переместите его в новую. Если указать только основной номер версии, приложение-функция автоматически обновится до новых дополнительных версий среды выполнения, когда они станут доступны. Новые дополнительные версии не должны вносить критические изменения. 

При указании дополнительного номера версии (например, "2.0.12345") приложение-функция будет закреплено за этой версией, пока вы явно не укажете другую. Более старые промежуточные версии регулярно удаляются из рабочей среды. После этого приложение функции будет работать в последней версии вместо версии, заданной в `FUNCTIONS_EXTENSION_VERSION` . В связи с этим следует быстро устранить все проблемы с приложением-функцией, для которых требуется определенная дополнительная версия, чтобы вместо нее можно было выбрать основную версию. Дополнительные удаления версий объявляются в [объявлениях службы приложений](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> Если вы запишете на конкретную основную версию функций Azure и попытаетесь опубликовать в Azure с помощью Visual Studio, откроется диалоговое окно с предложением выполнить обновление до последней версии или отменить публикацию. Чтобы избежать этого, добавьте `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` в `.csproj` файл свойство.

Если новая версия общедоступна, выполнить обновление до нее можно при помощи запроса на портале. Перейдя на новую версию, вы всегда сможете вернуться к предыдущей с помощью параметра приложения `FUNCTIONS_EXTENSION_VERSION`.

В следующей таблице показаны `FUNCTIONS_EXTENSION_VERSION` значения для каждой основной версии, позволяющие включить автоматическое обновление.

| Основной номер версии | Значение `FUNCTIONS_EXTENSION_VERSION` |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Изменение версии среды выполнения сопровождается перезапуском приложения-функции.

## <a name="view-and-update-the-current-runtime-version"></a>Просмотр и обновление текущей версии среды выполнения

Вы можете изменить версию среды выполнения, используемую приложением функции. Из-за возможных критических изменений можно изменить только версию среды выполнения до создания функций в приложении-функции. 

> [!IMPORTANT]
> Версию среды выполнения определяет параметр `FUNCTIONS_EXTENSION_VERSION`, но изменять это значение напрямую не следует. Вместо этого воспользуйтесь порталом Azure. Это обусловлено тем, что на портале изменения проверяются и при необходимости вносятся другие связанные с ними изменения.

# <a name="portal"></a>[Портал](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> С помощью портал Azure нельзя изменить версию среды выполнения для приложения-функции, которое уже содержит функции.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Параметр `FUNCTIONS_EXTENSION_VERSION` также можно просмотреть и задать с помощью интерфейса командной строки Azure.  

В Azure CLI просмотрите текущую версию среды выполнения с помощью команды [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

В этом коде следует заменить `<function_app>` именем приложения-функции. Также замените `<my_resource_group>` именем группы ресурсов для приложения-функции. 

В следующих выходных данных (сокращенных для ясности) есть параметр `FUNCTIONS_EXTENSION_VERSION`:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

Обновить параметр `FUNCTIONS_EXTENSION_VERSION` в приложении-функции можно с помощью команды [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Замените `<FUNCTION_APP>` на имя приложения-функции. Также замените `<RESOURCE_GROUP>` именем группы ресурсов для приложения-функции. Кроме того, замените `<VERSION>` либо определенной версией, либо `~3` , `~2` или `~1` .

Эту команду можно выполнить в [Azure Cloud Shell](../cloud-shell/overview.md), выбрав **Попробовать** в предыдущем примере кода. Также можно использовать [Azure CLI локально](/cli/azure/install-azure-cli) для выполнения этой команды после выполнения команды [az login](/cli/azure/reference-index#az-login) для входа.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Чтобы проверить среду выполнения функций Azure, используйте следующий командлет: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Замените `<FUNCTION_APP>` именем приложения функции и `<RESOURCE_GROUP>` . Текущее значение `FUNCTIONS_EXTENSION_VERSION` параметра возвращается в хэш-таблице.

Чтобы изменить среду выполнения функций, используйте следующий скрипт:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Как и ранее, замените `<FUNCTION_APP>` именем приложения функции и `<RESOURCE_GROUP>` именем группы ресурсов. Кроме того, замените на `<VERSION>` конкретную версию или основную версию, например `~2` или `~3` . Вы можете проверить обновленное значение `FUNCTIONS_EXTENSION_VERSION` параметра в возвращенной хэш-таблице. 

---

Приложение-функция перезапускается после внесения изменений в параметр приложения.

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Выбор среды выполнения 2.0 в локальной среде разработки](functions-run-local.md)

> [!div class="nextstepaction"]
> [См. заметки о выпуске для версий среды выполнения](https://github.com/Azure/azure-webjobs-sdk-script/releases)
