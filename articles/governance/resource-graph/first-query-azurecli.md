---
title: Выполнение запроса в интерфейсе командной строки Azure
description: Эта статья поможет выполнить шаги по включению расширения "Граф ресурсов" для Azure CLI и выполнению первого запроса.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 188733266c15bf214c0177762b9d10aecd05223b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792257"
---
# <a name="run-your-first-resource-graph-query-using-azure-cli"></a>Выполните первый запрос графа ресурсов с помощью Azure CLI

При использовании Azure Resource Graph в первую очередь необходимо проверить, установлено ли расширение для [Azure CLI](/cli/azure/). В этом кратком руководстве описывается процесс добавления расширения к установке Azure CLI. Можно использовать расширение Azure CLI, установленное локально или через [Azure Cloud Shell](https://shell.azure.com).

В конце этого процесса будет добавлено расширение к установке Azure CLI по выбору и выполнен первого запрос графа ресурсов.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="add-the-resource-graph-extension"></a>Добавление расширения "Граф ресурсов"

Чтобы позволить Azure CLI выполнять запрос графических ресурсов Azure, необходимо добавить расширение. Это расширение работает везде, где может использоваться оболочка Azure CLI, включая [bash на операционной системе Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (как автономно, так и внутри портала), [образ Azure CLI Docker](https://hub.docker.com/r/microsoft/azure-cli/) или установленную локально.

1. Убедитесь, что установлена последняя версия Azure CLI (не ниже **2.0.45**). Если она еще не установлена, выполните [эти инструкции](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. В выбранной вами среде Azure CLI импортируйте ее с помощью следующей команды:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Проверьте установку расширения и версию (по крайней мере **0.1.7**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Выполните первый запрос график ресурсов

Теперь, когда расширение для Azure CLI добавлено в выбранную среду, попробуем выполнить простой запрос к Resource Graph. Запрос возвращает первые пять ресурсов Azure с **Именем** и **Типом ресурса** для каждого ресурса.

1. Запустите ваш первый запрос графа ресурсов Azure, используя `graph` расширение и `query` команду :

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Так как этот пример запроса не меняет сортировку, например `order by`, выполнение этого запроса несколько раз может получить различные наборы ресурсов для каждого запроса.

1. Обновите запрос свойством `order by` **Имя**:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Как и с первым запросом, выполнение этого запроса несколько раз может получить различные наборы ресурсов для каждого запроса. Важен порядок команд запроса. В этом примере `order by` следует после `limit`. Это сначала ограничивает результаты запроса, а затем располагает их в нужном порядке.

1. Обновите запрос, чтобы сначала выполнить сортировку (`order by`) по свойству **Имя**, а затем ограничить (`limit`) вывод пятью первыми результатами:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'project name, type | order by name asc | limit 5'
   ```

Когда окончательный запрос выполняется несколько раз при условии, что ничего в вашей среде не изменяется, возвращаемые результаты будут последовательны и ожидаемы — упорядочены по свойству **Имя** и ограничены пятью первыми результатами.

## <a name="cleanup"></a>Очистка

Если вы хотите удалить расширение "Граф ресурсов" из среды Azure CLI, это можно сделать с помощью следующей команды:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

> [!NOTE]
> Это не приводит к удалению скачанного ранее файла расширения. Он удаляется только из открытого сеанса среды Azure CLI.

## <a name="next-steps"></a>Дополнительная информация

- Получите более подробную информацию о [языке запросов](./concepts/query-language.md)
- Узнайте больше о [просмотре ресурсов](./concepts/explore-resources.md).
- Выполните первый запрос с помощью [Azure PowerShell](first-query-powershell.md)
- См. примеры в разделе [начальных запросов](./samples/starter.md)
- Изучите примеры в разделе [Усложненные запросы](./samples/advanced.md).
- Оставьте отзыв о [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)