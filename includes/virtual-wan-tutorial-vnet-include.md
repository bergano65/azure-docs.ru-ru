---
title: включение файла
description: включение файла
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 04/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 40c8cb41ad3bcd46e9973a5f96134ff1bfd02fd2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150828"
---
Чтобы быстро создать свою виртуальную сеть, нажмите кнопку "Попробовать" в этой статье, чтобы открыть консоль PowerShell. Задайте значения, затем скопируйте и вставьте команды в окно консоли. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az см. в статье об [установке Azure PowerShell](/powershell/azure/install-az-ps).

Убедитесь, что адресное пространство для создаваемой виртуальной сети не пересекается с диапазонами адресов для других виртуальных сетей, к которым вы хотите подключиться, или с вашими локальными адресными пространствами сети.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Если у вас еще нет группы ресурсов для использования, создайте ее. Настройте команды PowerShell в соответствии с именем группы ресурсов, которую вы хотите использовать, а затем выполните следующий командлет:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Создание виртуальной сети

Настройте команды PowerShell, чтобы создать виртуальную сеть, совместимую с вашей средой.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```