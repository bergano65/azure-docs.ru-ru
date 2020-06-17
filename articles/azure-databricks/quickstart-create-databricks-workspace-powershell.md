---
title: Краткое руководство. Создание рабочей области Azure Databricks с помощью PowerShell
description: В этом кратком руководстве показано, как использовать PowerShell для создания рабочей области Azure Databricks.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485692"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>Краткое руководство. Создание рабочей области Azure Databricks с помощью PowerShell

В этом кратком руководстве описывается, как использовать PowerShell для создания рабочей области Azure Databricks. С помощью Azure PowerShell можно создавать и администрировать ресурсы Azure интерактивно или с помощью скриптов.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

Если вы решили использовать PowerShell локально, для работы с этой статьей установите модуль PowerShell Az и подключитесь к учетной записи Azure с помощью командлета [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount). См. сведения об [установке модуля Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Так как модуль PowerShell Az.Databricks предоставляется в предварительной версии, его нужно установить отдельно от модуля Az PowerShell с помощью следующей команды: `Install-Module -Name Az.Databricks -AllowPrerelease`.
> Как только модуль Az.Databricks PowerShell станет общедоступным, он будет включен в один из будущих выпусков модуля Az PowerShell и встроен в Azure Cloud Shell.

Если вы впервые используете Azure Databricks, зарегистрируйте поставщик ресурсов **Microsoft.Databricks**.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Если вы используете несколько подписок Azure, выберите ту, за ресурсы в которой будут выставляться счета. Выберите идентификатор требуемой подписки с помощью командлета [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа.

В следующем примере создается группа ресурсов с именем **myresourcegroup** в регионе **Западная часть США 2**.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>Создание рабочей области Azure Databricks

В этом разделе вы создадите рабочую область Azure Databricks с помощью PowerShell.

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

Укажите следующие значения.

|       **Свойство**       |                                                                                **Описание**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Имя                     | Укажите имя рабочей области Databricks.                                                                                                                                   |
| ResourceGroupName        | Укажите имя существующей группы ресурсов                                                                                                                                        |
| Расположение                 | Выберите **Западная часть США 2**. Другие доступные регионы см. в статье о [доступных службах Azure по регионам](https://azure.microsoft.com/regions/services/).                                     |
| ManagedResourceGroupName | Укажите, следует ли создать новую управляемую группу ресурсов или использовать имеющуюся.                                                                                        |
| Sku                      | Вы можете выбрать уровень **Стандартный** или **Премиум** или воспользоваться **бесплатной пробной версией**. Дополнительные сведения об этих ценовых категориях см. в разделе [Цены на Databricks](https://azure.microsoft.com/pricing/details/databricks/). |

Создание рабочей области займет несколько минут. После завершения этого процесса учетная запись пользователя автоматически добавляется в рабочую область в качестве учетной записи администратора.

Если при развертывании рабочей области произойдет сбой, она все равно будет создана, но в состоянии сбоя. Удалите такую рабочую область и создайте новую, чтобы устранить ошибки с развертыванием. При удалении рабочей области, для которой произошел сбой, также удаляются ее управляемую группу ресурсов и все успешно развернутые ресурсы.

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>Определение состояния подготовки рабочей области Databricks

Чтобы определить, успешно ли подготовлена рабочая область Databricks, можно использовать командлет `Get-AzDatabricksWorkspace`.

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если созданные в этом кратком руководстве ресурсы не нужны вам для другого руководства, их можно удалить с помощью команды из следующего примера.

> [!CAUTION]
> Следующий пример удаляет указанную группу ресурсов и все содержащиеся в ней ресурсы.
> Если в указанной группе ресурсов существуют другие ресурсы, кроме созданных для этого краткого руководства, они также будут удалены.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Чтобы удалить только тот сервер, который вы создали с помощью этого краткого руководства, но сохранить группу ресурсов, используйте командлет `Remove-AzDatabricksWorkspace`.

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание кластера Spark в Databricks](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
