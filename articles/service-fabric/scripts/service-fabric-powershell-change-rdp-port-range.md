---
title: Пример сценария Azure PowerShell для изменения диапазона портов RDP | Документация Майкрософт
description: Пример сценария Azure PowerShell для изменения диапазона портов RDP развернутого кластера.
services: service-fabric
tags: azure-service-management
author: athinanthny
ms.author: atsenthi
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.custom: sfrev
ms.openlocfilehash: b3d922cb5a7cdf34e63731ab32f21c94d7b6bca0
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76025018"
---
# <a name="update-the-rdp-port-range-values"></a>Обновление значений диапазона портов RDP

Этот сценарий изменяет значения диапазона портов RDP на виртуальных машинах узла кластера после развертывания кластера.  Используется Azure PowerShell, чтобы порты базовых виртуальных машин не сменялись циклически.  Скрипт получает ресурс `Microsoft.Network/loadBalancers` в группе ресурсов кластера и обновляет значения `inboundNatPools.frontendPortRangeStart` и `inboundNatPools.frontendPortRangeEnd`. Измените параметры, если это необходимо.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве по Azure PowerShell](/powershell/azure/overview).

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Возвращает ресурс `Microsoft.Network/loadBalancers`. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Обновляет ресурс `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).
