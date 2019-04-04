---
title: Пример сценария Azure PowerShell — создание приложения Service Fabric | Документация Майкрософт
description: Пример сценария Azure PowerShell — создание приложения Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 8a291e49272c47035f73ad70534ac5393060940a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663559"
---
# <a name="upgrade-a-service-fabric-application"></a>Обновление приложения Service Fabric

Этот сценарий обновляет запущенный экземпляр приложения Service Fabric до версии 1.3.0. Сценарий копирует новый пакет приложения в хранилище образов кластера, регистрирует тип приложения и удаляет ненужные пакеты приложения.  Сценарий начинает отслеживаемое обновление и постоянно проверяет состояние обновления до его завершения либо выполняет откат. Измените параметры, если это необходимо. 

При необходимости установите модуль PowerShell ServiceFabric вместе с [пакетом SDK для Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Возвращает все приложения в кластер Service Fabric или конкретное приложение.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Возвращает состояние обновления приложения Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Возвращает типы приложений Service Fabric, зарегистрированные в кластере Service Fabric. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Отменяет регистрацию типа приложения Service Fabric.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Копирует пакет приложения Service Fabric в хранилище образов.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Регистрирует тип приложения Service Fabric. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Обновляет приложение Service Fabric до указанной версии типа приложения. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Удаляет пакет приложения Service Fabric из хранилища образов.|


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Service Fabric PowerShell см. в [документации по Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Дополнительные примеры сценариев PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).
