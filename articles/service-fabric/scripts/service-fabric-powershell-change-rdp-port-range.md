---
title: Пример сценария Azure PowerShell для изменения диапазона портов RDP | Документация Майкрософт
description: Пример сценария Azure PowerShell для изменения диапазона портов RDP развернутого кластера.
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
ms.date: 03/19/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: ee2ac3a2051ba7dd63aac5928e1713541f23b81f
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500106"
---
# <a name="update-the-rdp-port-range-values"></a>Обновление значений диапазона портов RDP

Этот сценарий изменяет значения диапазона портов RDP на виртуальных машинах узла кластера после развертывания кластера.  Используется Azure PowerShell, чтобы порты базовых виртуальных машин не сменялись циклически.  Сценарий возвращает ресурс `Microsoft.Network/loadBalancers` в группе ресурсов кластера и обновляет значения `inboundNatPools.frontendPortRangeStart` и `inboundNatPools.frontendPortRangeEnd`. Измените параметры, если это необходимо.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве по Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Возвращает ресурс `Microsoft.Network/loadBalancers`. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Обновляет ресурс `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).
