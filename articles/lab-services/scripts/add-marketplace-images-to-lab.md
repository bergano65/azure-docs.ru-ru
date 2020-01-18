---
title: PowerShell. Добавление образа Marketplace в лабораторию в Azure DevTest Labs
description: В этом сценарии PowerShell показано добавление образа marketplace в лабораторию в Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d699a22ed06022c6d9df12ade7f202fb304648c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166449"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Добавление образа marketplace в лабораторию в Azure DevTest Labs с помощью PowerShell

В этом примере сценария PowerShell показано добавление образа marketplace в лабораторию в Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Технические условия
* **Лаборатория.** Этот сценарий требует наличия лаборатории. 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Get-Help | Примечания |
|---|---|
| Find-Азресаурце | Выполняет поиск ресурсов на основе указанных параметров. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Получает ресурсы. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Изменяет ресурс. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Создает ресурса. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для службы "Службы лабораторий Azure" приведены [здесь](../samples-powershell.md).
