---
title: Повторное развертывание виртуальных машин Windows в Azure | Документация Майкрософт
description: Повторное развертывание виртуальных машин Windows в Azure для устранения проблем с подключением к удаленному рабочему столу.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 5da5cfebfb3f847f01165aa28309a44e62ef96a3
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418778"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Повторное развертывание виртуальной машины Windows на новом узле Azure
Если вам не удается подключиться к удаленному рабочему столу или получить доступ к приложению на виртуальной машине Windows Azure, можно попробовать повторно развернуть виртуальную машину. При повторном развертывании виртуальной машины Azure попытается корректно завершить работу виртуальной машины, переместить ее на новый узел в рамках инфраструктуры Azure, а затем снова включить с сохранением всех параметров конфигурации и связанных ресурсов. В этой статье показано, как повторно развернуть виртуальную машину с помощью Azure PowerShell или портала Azure.

> [!NOTE]
> После развертывания временный диск будет удален, а связанные с виртуальным сетевым интерфейсом динамические IP-адреса будут обновлены. 


## <a name="using-azure-powershell"></a>Использование Azure PowerShell
Убедитесь, что на компьютере установлена последняя версия Azure PowerShell 1.x. Подробнее: [Установка и настройка Azure PowerShell](/powershell/azure/overview).

В следующем примере развертывается виртуальная машина `myVM` в группе ресурсов `myResourceGroup`.

```powershell
Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Дополнительная информация
При проблемах с подключением к виртуальной машине ознакомьтесь со статьями [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](troubleshoot-rdp-connection.md) и [Подробная диагностика подключений к удаленному рабочему столу виртуальной машины Azure под управлением Windows](detailed-troubleshoot-rdp.md). При проблемах с доступом к приложению, выполняющемуся на виртуальной машине, ознакомьтесь со статьей [Устранение неполадок доступа к приложению, выполняющемуся в виртуальной машине Azure](../windows/troubleshoot-app-connection.md).

