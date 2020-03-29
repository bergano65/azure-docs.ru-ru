---
title: Сценарий PowerShell для задания допустимых размеров виртуальных машин в службе "Службы лабораторий Azure" | Документация Майкрософт
description: Эта статья включает в себя образец сценария PowerShell, который устанавливает разрешенные размеры виртуальной машины (VM) в Службах лаборатории Azure.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a1b0e9a4aed475f04ec8dcffa9bc95b7c7c713e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760473"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Задание допустимых размеров виртуальных машин в службе "Службы лабораторий Azure" с помощью PowerShell

Этот пример сценария PowerShell задает допустимые размеры виртуальных машин в службе "Службы лабораторий Azure".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Предварительные требования
* **Лаборатория.** Этот сценарий требует наличия лаборатории. 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Команда | Примечания |
|---|---|
| Найти-AzResource | Выполняет поиск ресурсов на основе указанных параметров. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Получает ресурсы. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Изменяет ресурс. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Создает ресурса. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для службы "Службы лабораторий Azure" приведены [здесь](../samples-powershell.md).
