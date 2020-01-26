---
title: PowerShell. Создание пользовательской роли в лаборатории в Azure DevTest Labs
description: В этой статье представлен сценарий Azure PowerShell, который добавляет внешнего пользователя в лабораторию в Azure DevTest Labs.
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
ms.openlocfilehash: 9605b3c63877335b8f180ae5fd2b06edc1601096
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760472"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Создание настраиваемой роли в лаборатории Azure DevTest Labs с помощью PowerShell

Этот пример сценария PowerShell создает настраиваемую роль для использования в лаборатории Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Технические условия
* **Лаборатория.** Этот сценарий требует наличия лаборатории. 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Get-Help | Примечания |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Возвращает операции для поставщика ресурсов Azure, защищаемые с помощью Azure RBAC. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Выводит список всех ролей Azure RBAC, доступных для назначения. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Создает настраиваемую роль. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для службы "Службы лабораторий Azure" приведены [здесь](../samples-powershell.md).
