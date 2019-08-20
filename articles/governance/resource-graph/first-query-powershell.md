---
title: Выполнение запроса с помощью Azure PowerShell
description: Эта статья поможет выполнить шаги по включению модуля "График ресурсов" для Azure PowerShell и выполнению первого запроса.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: a8221de15dfa48b4eebcf0fe0df29ea0c3a1868e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854968"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Краткое руководство. Выполните первый запрос графика ресурсов с помощью Azure PowerShell

При использовании Azure Resource Graph прежде всего необходимо убедиться, что модуль для Azure PowerShell установлен. В этом кратком руководстве описывается процесс добавления модуля к установке Azure PowerShell.

В конце этого процесса вы добавите модуль к установке Azure PowerShell на свой выбор и выполните первый запрос Resource Graph.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="add-the-resource-graph-module"></a>Добавить модуль "График ресурсов"

Чтобы позволить Azure PowerShell выполнять запрос графических ресурсов Azure, необходимо добавить модуль. Этот модуль можно использовать с локально установленной средой PowerShell, с [Azure Cloud Shell](https://shell.azure.com) либо с [образом Docker PowerShell](https://hub.docker.com/_/microsoft-powershell).

### <a name="base-requirements"></a>Основные требования

Модуль Azure Resource Graph требует следующее программное обеспечение.

- Azure PowerShell 1.0.0 или более поздней версии. Если она еще не установлена, выполните [эти инструкции](/powershell/azure/install-az-ps).

- Модуль PowerShellGet 2.0.1 или более поздней версии. Если он еще не установлен или не обновлен, выполните [эти инструкции](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Установка модуля

Модуль Resource Graph для PowerShell — **Az.ResourceGraph**.

1. Запустите следующие команды из командной строки PowerShell **с правами администратора**.

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Убедитесь, что модуль импортирован и имеет требуемую версию (0.7.3).

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Выполните первый запрос график ресурсов

Теперь, когда модуль PowerShell для Azure добавлен в выбранную среду, попробуем выполнить простой запрос Resource Graph. Запрос возвращает первые пять ресурсов Azure с **Именем** и **Типом ресурса** для каждого ресурса.

1. Запустите ваш первый запрос графика ресурсов Azure, используя командлет `Search-AzGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Так как этот пример запроса не меняет сортировку, например `order by`, выполнение этого запроса несколько раз может получить различные наборы ресурсов для каждого запроса.

1. Обновите запрос свойством `order by` **Имя**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Как и с первым запросом, выполнение этого запроса несколько раз может получить различные наборы ресурсов для каждого запроса. Важен порядок команд запроса. В этом примере `order by` следует после `limit`. Это сначала ограничивает результаты запроса, а затем располагает их в нужном порядке.

1. Обновите запрос, чтобы сначала выполнить сортировку (`order by`) по свойству **Имя**, а затем ограничить (`limit`) вывод пятью первыми результатами:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Когда окончательный запрос выполняется несколько раз при условии, что ничего в вашей среде не изменяется, возвращаемые результаты будут последовательны и ожидаемы — упорядочены по свойству **Имя** и ограничены пятью первыми результатами.

> [!NOTE]
> Если запрос не возвращает результаты из подписки, к которой у вас уже есть доступ, имейте ввиду, что командлет `Search-AzGraph` использует значения по умолчанию для подписок в контексте по умолчанию. Чтобы просмотреть список идентификаторов подписок, которые входят в контекст по умолчанию, выполните `(Get-AzContext).Account.ExtendedProperties.Subscriptions`. Если требуется выполнить поиск во всех подписках, к которым у вас есть доступ, можно задать PSDefaultParameterValues для командлета `Search-AzGraph`, выполнив команду `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`.
   
## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите удалить модуль "График ресурсов" из среды Azure PowerShell, это можно сделать с помощью следующей команды:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Это не приводит к удалению скачанного ранее файла модуля. Он удаляется только из открытого сеанса PowerShell.

## <a name="next-steps"></a>Дополнительная информация

- Получите более подробную информацию о [языке запросов](./concepts/query-language.md)
- Подробнее о [просмотре ресурсов](./concepts/explore-resources.md)
- Выполните первый запрос с помощью [Azure CLI](first-query-azurecli.md)
- См. примеры в разделе [начальных запросов](./samples/starter.md)
- Изучите примеры в разделе [Усложненные запросы](./samples/advanced.md).
- Оставьте отзыв о [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)