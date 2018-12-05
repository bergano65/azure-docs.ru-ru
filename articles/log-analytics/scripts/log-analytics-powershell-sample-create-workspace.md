---
title: 'Пример сценария Azure PowerShell: создание рабочей области Log Analytics | Документы Майкрософт'
description: 'Пример сценария Azure PowerShell: создание рабочей области Log Analytics'
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 5ad04c52da4709a7097ff7915d7af7404d6725eb
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52680265"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Создание рабочей области Log Analytics с помощью PowerShell

Этот сценарий позволяет быстро создать и подготовить рабочую область Azure Log Analytics, которая необходима для сбора и анализа данных и выполнения действий на их основе.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды для создания новой рабочей области Log Analytics в вашей подписке. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) | Получает сведения о существующей рабочей области. |
| [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) | Создает рабочую область в указанной группе ресурсов и расположении. |


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

