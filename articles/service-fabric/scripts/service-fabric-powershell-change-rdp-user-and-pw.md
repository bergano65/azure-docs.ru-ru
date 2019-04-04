---
title: Пример сценария Azure PowerShell для обновления имени пользователя и пароля RDP | Документация Майкрософт
description: Пример сценария Azure PowerShell для обновления имени пользователя и пароля RDP для всех узлов кластера Service Fabric, относящихся к конкретному типа узла.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
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
ms.openlocfilehash: 1528ce03f548c70d20d8f7af0238019305f01f2e
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660822"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Обновление имени пользователя и пароля администратора виртуальных машин в кластере

Масштабируемый набор виртуальных машин соответствует [типу узла](../service-fabric-cluster-nodetypes.md) в кластере Service Fabric. Этот сценарий обновляет имя пользователя и пароль администратора для виртуальных машин кластера на узле определенного типа.  Добавьте расширение VMAccessAgent в масштабируемый набор, так как пароль администратора является неизменяемым свойством масштабируемого набора.  Изменение имени пользователя и пароля применяется ко всем узлам в масштабируемом наборе. Измените параметры, если это необходимо.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве по Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Получает свойства типа узла кластера (масштабируемого набора виртуальных машин).   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Добавляет расширение в масштабируемый набор виртуальных машин.|
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss)|Обновляет состояние масштабируемого набора виртуальных машин до состояния локального объекта VMSS.|

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).
