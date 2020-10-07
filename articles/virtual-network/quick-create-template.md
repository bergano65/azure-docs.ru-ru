---
title: Краткое руководство. Создание виртуальной сети с использованием шаблона Resource Manager
titleSuffix: Azure Virtual Network
description: Сведения об использовании шаблона Resource Manager для создания виртуальной сети Azure.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: c9e9ea2e59537a1c0c8e372a766fba3aa9a1b9a0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88122162"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>Краткое руководство. Создание виртуальной сети с использованием шаблона Resource Manager

Из этого краткого руководства вы узнаете, как создать виртуальную сеть с двумя подсетями, используя шаблон Azure Resource Manager. Виртуальная сеть — это стандартный строительный блок для вашей частной сети в Azure. Виртуальная сеть позволяет ресурсам Azure, таким как виртуальные машины, безопасно обмениваться данными друг с другом и взаимодействовать через Интернет.


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Инструкции в этом кратком руководстве можно также выполнить с помощью [портала Azure](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md) или [Azure CLI](quick-create-cli.md).

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

В шаблоне определены такие ресурсы Azure:
- [**Microsoft.Network/virtualNetworks**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks): создание виртуальной сети Azure.
-  [**Microsoft.Network/virtualNetworks/subnets**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets): создание подсети.

## <a name="deploy-the-template"></a>Развертывание шаблона

Развертывание шаблона Resource Manager в Azure:

1. Выберите элемент **Развертывание в Azure**, чтобы войти в Azure и открыть шаблон. Шаблон создает виртуальную сеть с двумя подсетями.

   [![Развертывание в Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. На портале на странице **Создание виртуальной сети с двумя подсетями** введите или выберите следующие значения:
   - **Группа ресурсов.** Выберите **Create new** (Создать новую), введите имя группы ресурсов и выберите **ОК**.
   - **Имя виртуальной сети**. Введите имя для новой виртуальной сети.
3. Выберите **Просмотр и создание** и затем **Создать**.

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

Изучите ресурсы, созданные с помощью виртуальной сети.

Дополнительные сведения о синтаксисе JSON и свойствах для виртуальной сети в шаблоне см. в разделе [Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам уже не нужны ресурсы, созданные с помощью виртуальной сети, удалите группу ресурсов. При этом будет удалена виртуальная сеть и все связанные с ней ресурсы.

Чтобы удалить группу ресурсов, вызовите командлет `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Дальнейшие действия
В этом кратком руководстве вы развернули виртуальную сеть Azure с двумя подсетями. Дополнительные сведения о виртуальных сетях Azure см. в руководстве по работе с виртуальными сетями.

> [!div class="nextstepaction"]
> [Фильтрация сетевого трафика](tutorial-filter-network-traffic.md)
