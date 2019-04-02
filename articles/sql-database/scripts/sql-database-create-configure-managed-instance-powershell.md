---
title: Пример для PowerShell. Создание управляемого экземпляра в базе данных SQL Azure | Документация Майкрософт
description: Пример сценария Azure PowerShell для создания управляемого экземпляра в базе данных SQL Azure
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 955f13376ac899f66b0ec4e1ed99166164508fbe
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449817"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Создание управляемого экземпляра Базы данных SQL Azure с помощью PowerShell

Этот пример сценария PowerShell создает управляемый экземпляр Базы данных SQL Azure в выделенной подсети в новой виртуальной сети. Он также настраивает таблицу маршрутов и группу безопасности сети для виртуальной сети. После успешного выполнения сценария доступ к управляемому экземпляру можно получить из виртуальной сети или из локальной среды. Краткие инструкции о выполнении этих операций см. в статье о [настройке виртуальной машины Azure для подключения к Управляемому экземпляру Базы данных SQL Azure](../sql-database-managed-instance-configure-vm.md), а также в статье о [настройке подключения "точка — сеть" к Управляемому экземпляру Базы данных SQL Azure из локальной среды](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Сведения об ограничениях см. в разделах [Поддерживаемые регионы](../sql-database-managed-instance-resource-limits.md#supported-regions) и [Поддерживаемые типы подписок](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, вам понадобится AZ PowerShell 1.4.0 или последующей версии для работы с этим руководством. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="sample-script"></a>Пример скрипта

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. 
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Создает виртуальную сеть. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Добавляет конфигурацию подсети в виртуальную сеть. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Получает виртуальную сеть в группе ресурсов. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Задает состояние цели для виртуальной сети. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Получает подсеть в виртуальной сети. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Настраивает состояние цели для конфигурации подсети в виртуальной сети. |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Создает таблицу маршрутов. |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Получает таблицы маршрутов. |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Задает состояние цели для таблицы маршрутов. |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Создает управляемый экземпляр Базы данных SQL Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).
