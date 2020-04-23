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
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: d2f5d6b6b9500ccd90630e8920c09340658fb76f
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100198"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Добавление образа marketplace в лабораторию в Azure DevTest Labs с помощью PowerShell

В этом примере сценария PowerShell показано добавление образа marketplace в лабораторию в Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Предварительные требования
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
