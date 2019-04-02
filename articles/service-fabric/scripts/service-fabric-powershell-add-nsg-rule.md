---
title: Пример сценария Azure PowerShell для добавления правила группы безопасности сети | Документация Майкрософт
description: Пример сценария Azure PowerShell для добавления группы безопасности сети, разрешающей входящий трафик через определенный порт.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: fead6aa19775eab6cc3a1014e9f52b30cfa2cefa
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498321"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Добавление правило входящего трафика для группы безопасности сети

Этот пример сценария создает правило группы безопасности сети, разрешающее входящий трафик для порта 8081.  Сценарий возвращает ресурс `Microsoft.Network/networkSecurityGroups`, в котором находится кластер, создает правило конфигурации безопасности сети и обновляет группу безопасности сети. Измените параметры, если это необходимо.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

При необходимости установите Azure PowerShell с помощью инструкций, приведенных в [руководстве по Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Возвращает ресурс `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Возвращает группу безопасности сети по имени.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Добавляет в группу безопасности сети конфигурацию правила безопасности сети. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Задает целевое состояние для группы безопасности сети.|

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).
