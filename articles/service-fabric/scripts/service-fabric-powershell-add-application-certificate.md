---
title: Пример сценария Azure PowerShell — добавление сертификата приложения в кластер | Документы Майкрософт
description: Пример сценария Azure PowerShell — добавление сертификата приложения в кластер Service Fabric.
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
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 3d2ab7339a641164a628854c00e22f437b21c3df
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670461"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Добавление сертификата приложения в кластер Service Fabric

В этом примере сценария создается самозаверяющий сертификат из указанного хранилища ключей Azure. Затем этот сертификат устанавливается на все узлы кластера Service Fabric. Сертификат также скачивается в локальную папку. Имя скачанного сертификата совпадает с именем сертификата в хранилище ключей. Измените параметры, если это необходимо.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве по Azure PowerShell](/powershell/azure/overview), а затем выполните команду `Connect-AzAccount`, чтобы создать подключение к Azure. 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Add an application certificate to a cluster")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Add-AzServiceFabricApplicationCertificate](/powershell/module/az.servicefabric/Add-azServiceFabricApplicationCertificate) | Добавляет новый сертификат приложения в масштабируемый набор виртуальных машин, входящих в состав кластера.  |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).
