---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 872164dfee9f35881fca97b0339bd95c64a2f5ff
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070220"
---
Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. С помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell создайте группу ресурсов с именем *myResourceGroup* в регионе *eastus*. 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```
