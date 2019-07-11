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
ms.openlocfilehash: 40ae634897361219c39e60d2161d3576cc44a400
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077529"
---
Чтобы быстро создать виртуальную сеть, нажмите кнопку "Попробовать" в этой статье, чтобы открыть консоль PowerShell в Azure Cloud Shell. Задайте значения, затем скопируйте и вставьте команды в окно консоли. 

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
