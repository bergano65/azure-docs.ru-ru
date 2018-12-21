---
title: Выполнение запроса с помощью Azure PowerShell
description: Эта статья поможет выполнить шаги по включению модуля "График ресурсов" для Azure PowerShell и выполнению первого запроса.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/27/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 5ffc93afdfff1a069d00b61868b5ae025121198c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310731"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Выполните первый запрос графика ресурсов с помощью Azure PowerShell

При использовании Azure Resource Graph прежде всего необходимо убедиться, что модуль для Azure PowerShell установлен. В этом кратком руководстве описывается процесс добавления модуля к установке Azure PowerShell.

В конце этого процесса вы добавите модуль к установке Azure PowerShell на свой выбор и выполните первый запрос Resource Graph.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="add-the-resource-graph-module"></a>Добавить модуль "График ресурсов"

Чтобы позволить Azure PowerShell выполнять запрос графических ресурсов Azure, необходимо добавить модуль. Этот модуль можно использовать с локально установленной средой Windows PowerShell и PowerShell Core либо с [образом Docker Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Основные требования

Модуль Azure Resource Graph требует следующее программное обеспечение.

- Azure PowerShell версии 6.3.0 и более поздней. Если она еще не установлена, выполните [эти инструкции](/powershell/azure/install-azurerm-ps).

  - Для PowerShell Core используйте версию модуля Azure PowerShell **Az**.

  - Для Windows PowerShell используйте версию модуля Azure PowerShell **AzureRm**.

- Модуль PowerShellGet 2.0.1 или более поздней версии. Если он еще не установлен или не обновлен, выполните [эти инструкции](/powershell/gallery/installing-psget).

### <a name="cloud-shell"></a>Cloud Shell

Чтобы добавить модуль Azure Resource Graph в Cloud Shell, выполните приведенные ниже инструкции для PowerShell Core.

### <a name="powershell-core"></a>PowerShell Core

Модуль Resource Graph для PowerShell Core — **Az.ResourceGraph**.

1. Запустите следующие команды из командной строки PowerShell **с правами администратора**.

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Убедитесь, что модуль был импортирован и имеет требуемую версию (0.3.0).

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

1. Обеспечьте обратные псевдонимы для **Az** для **AzureRm**, выполнив команду приведенную ниже.

   ```azurepowershell-interactive
   # Enable backwards alias compatibility
   Enable-AzureRmAlias
   ```

### <a name="windows-powershell"></a>Windows PowerShell

Модуль Resource Graph для Windows PowerShell — **AzureRm.ResourceGraph**.

1. Запустите следующие команды из командной строки Windows PowerShell **с правами администратора**.

   ```powershell
   # Install the Resource Graph (prerelease) module from PowerShell Gallery
   Install-Module -Name AzureRm.ResourceGraph -AllowPrerelease
   ```

1. Убедитесь, что модуль был импортирован и имеет требуемую версию (0.1.1-preview).

   ```powershell
   # Get a list of commands for the imported AzureRm.ResourceGraph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Выполните первый запрос график ресурсов

Теперь, когда модуль PowerShell для Azure добавлен в выбранную среду, попробуем выполнить простой запрос Resource Graph. Запрос возвращает первые пять ресурсов Azure с **Именем** и **Типом ресурса** для каждого ресурса.

1. Запустите ваш первый запрос графика ресурсов Azure, используя командлет `Search-AzureRmGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzureRmAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Так как этот пример запроса не меняет сортировку, например `order by`, выполнение этого запроса несколько раз может получить различные наборы ресурсов для каждого запроса.

1. Обновите запрос свойством `order by` **Имя**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > Как и с первым запросом, выполнение этого запроса несколько раз может получить различные наборы ресурсов для каждого запроса. Важен порядок команд запроса. В этом примере `order by` следует после `limit`. Это сначала ограничивает результаты запроса, а затем располагает их в нужном порядке.

1. Обновите запрос, чтобы сначала выполнить сортировку (`order by`) по свойству **Имя**, а затем ограничить (`limit`) вывод пятью первыми результатами:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Когда окончательный запрос выполняется несколько раз при условии, что ничего в вашей среде не изменяется, возвращаемые результаты будут последовательны и ожидаемы — упорядочены по свойству **Имя** и ограничены пятью первыми результатами.

## <a name="cleanup"></a>Очистка

Если вы хотите удалить модуль "График ресурсов" из среды Azure PowerShell, это можно сделать с помощью следующей команды:

```powershell
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'AzureRm.ResourceGraph'
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