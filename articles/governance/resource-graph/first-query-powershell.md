---
title: Выполните первый запрос графика ресурсов с помощью Azure PowerShell
description: Эта статья поможет выполнить шаги по включению модуля "График ресурсов" для Azure PowerShell и выполнению первого запроса.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 1a2bc5626e94f5fcb0ec8c2be8d91c8fc6484e0b
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224568"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Выполните первый запрос графика ресурсов с помощью Azure PowerShell

Первым шагом при использовании графика ресурсов Azure необходимо убедиться, что установлен модуль для Azure PowerShell. В этом кратком руководстве описывается процесс добавления модуля к установке Azure PowerShell.

В конце этого процесса будет добавлен модуль к установке Azure PowerShell, по выбору и выполнению самого первого запроса графика ресурсов.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="add-the-resource-graph-module"></a>Добавить модуль "График ресурсов"

Чтобы позволить Azure PowerShell выполнять запрос графических ресурсов Azure, необходимо добавить модуль. Этот модуль можно использовать с локально установленного Windows PowerShell и PowerShell Core, а также [образа Docker в Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Основные требования

Модуль Azure Resource Graph требует следующее программное обеспечение.

- Azure PowerShell версии 6.3.0 и более поздней. Если она еще не установлена, выполните [эти инструкции](/powershell/azure/install-azurerm-ps).

  - Для PowerShell Core используйте версию модуля Azure PowerShell **Az**.

  - Для Windows PowerShell используйте версию модуля Azure PowerShell **AzureRm**.

  > [!NOTE]
  > В настоящее время не рекомендуется устанавливать модуль в Cloud Shell.

- PowerShellGet. Если она еще не установлена или не обновлена, выполните [эти инструкции](/powershell/gallery/installing-psget).

### <a name="powershell-core"></a>PowerShell Core

Модуль Resource Graph для PowerShell Core — **Az.ResourceGraph**.

1. Запустите следующие команды из командной строки PowerShell **с правами администратора**.

   ```powershell
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Проверьте, что модуль был импортирован и имеет требуемую версию (0.2.0).

   ```powershell
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

1. Обеспечьте обратные псевдонимы для **Az** для **AzureRm**, выполнив команду приведенную ниже.

   ```powershell
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

1. Проверьте, что модуль был импортирован и имеет требуемую версию (0.1.0).

   ```powershell
   # Get a list of commands for the imported AzureRm.ResourceGraph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Выполните первый запрос график ресурсов

Теперь, когда модуль PowerShell для Azure был добавлен в выбранную среду, настала пора попробовать простой запрос графика ресурсов. Запрос возвращает первые пять ресурсов Azure с **Именем** и **Типом ресурса** для каждого ресурса.

1. Запустите ваш первый запрос графика ресурсов Azure, используя командлет `Search-AzureRmGraph`:

   ```powershell
   # Login first with Connect-AzureRmAccount

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Так как этот пример запроса не меняет сортировку, например `order by`, выполнение этого запроса несколько раз может получить различные наборы ресурсов для каждого запроса.

1. Обновите запрос свойством `order by` **Имя**:

   ```powershell
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > Как и с первым запросом, выполнение этого запроса несколько раз может получить различные наборы ресурсов для каждого запроса. Важен порядок команд запроса. В этом примере `order by` следует после `limit`. Это сначала ограничивает результаты запроса, а затем располагает их в нужном порядке.

1. Обновите запрос сначала `order by` свойства **Имя**, а затем `limit` 5 лучшими результатами:

   ```powershell
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Когда окончательный запрос выполняется несколько раз, при условии, что ничего в вашей среде не изменяется, возвращаемые результаты будут последовательны и ожидаемы — упорядочены по свойству **Имя** и ограничены 5 лучшими результатами.

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