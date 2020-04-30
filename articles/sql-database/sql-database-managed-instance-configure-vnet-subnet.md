---
title: Настройка существующей виртуальной сети для управляемого экземпляра
description: В этой статье описывается, как настроить имеющуюся виртуальную сеть и подсеть, в которой можно развернуть Управляемый экземпляр Базы данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 50b832baa9253f47b5f10980ae1764c9425ed4d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476955"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Настройка имеющейся виртуальной сети для Управляемого экземпляра Базы данных SQL Azure

Управляемый экземпляр Базы данных SQL Azure нужно развернуть в [виртуальной сети](../virtual-network/virtual-networks-overview.md) и подсети Azure, выделенных только для Управляемых экземпляров. Вы можете использовать имеющуюся виртуальную сеть и подсеть, если она настроена в соответствии с [требованиями к виртуальной сети Управляемого экземпляра](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Если к вам применим один из следующих вариантов, вы можете проверить и изменить свою сеть с помощью сценария, описанного в этой статье:

- У вас есть новая подсеть, которая все еще не настроена.
- Вы не уверены, что подсеть соответствует [требованиям](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Вы хотите убедиться, что подсеть по-прежнему соответствует [требованиям к сети](sql-database-managed-instance-connectivity-architecture.md#network-requirements) после внесения изменений.

> [!Note]
> Управляемый экземпляр можно создать только в виртуальных сетях, созданных с помощью модели развертывания Azure Resource Manager. Пиринг между виртуальными сетями, созданными с помощью классической модели развертывания, невозможен. Вычислите размер подсети, следуя инструкциям из статьи [Определение размера подсети виртуальной сети для Управляемого экземпляра Базы данных SQL Azure](sql-database-managed-instance-determine-size-vnet-subnet.md). Вы не можете изменить размер подсети после того, как развернете в ней ресурсы.
>
> После создания управляемого экземпляра перемещение управляемого экземпляра или виртуальной сети в другую группу ресурсов или подписку не поддерживается.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Проверка и изменение имеющейся виртуальной сети

Если вы хотите создать Управляемый экземпляр внутри имеющейся подсети, мы рекомендуем использовать следующий сценарий PowerShell для подготовки подсети.

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Сценарий подготавливает подсеть в три этапа:

1. Проверка: проверяет выбранную виртуальную сеть и подсеть для Управляемый экземпляр требований к сети.
2. Подтверждение: в нем отображается набор изменений, которые необходимо внести для подготовки подсети к развертыванию Управляемый экземпляр. Кроме того, в процессе выдается запрос на согласие.
3. Подготовка. она правильно настраивает виртуальную сеть и подсеть.

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения см. в статье [Использование Управляемого экземпляра Базы данных SQL с виртуальными сетями и почти полной совместимостью](sql-database-managed-instance.md).
- Чтобы узнать, как создать виртуальную сеть и Управляемый экземпляр, а также восстановить базу данных из резервной копии базы данных, ознакомьтесь со статьей [Краткое руководство. Создание Управляемого экземпляра Базы данных SQL Azure](sql-database-managed-instance-get-started.md).
- Сведения о проблемах DNS см. [в разделе Настройка настраиваемой службы DNS](sql-database-managed-instance-custom-dns.md).
