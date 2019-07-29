---
title: Пример скрипта для удаления приложения с помощью командной строки Azure Service Fabric (sfctl)
description: Удаления приложения из кластера Azure Service Fabric с помощью интерфейса командной строки Azure Service Fabric
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
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 7c3739a2e5e15e77cb88ffb9d3effe9abdd0b848
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592233"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Удаление приложения из кластера Service Fabric

Этот скрипт удаляет запущенный экземпляр приложения Service Fabric и отменяет регистрацию типа и версии приложения в кластере.  При удалении экземпляра приложения также удаляются все выполняющиеся экземпляры службы, связанные с этим приложением. Затем из хранилища образов удаляются файлы приложения. 

При необходимости установите [интерфейс командной строки Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Пример скрипта

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в [документации интерфейса командной строки Service Fabric](../service-fabric-cli.md).

Дополнительные примеры сценариев интерфейса командной строки Service Fabric для Azure Service Fabric см. в статье [Примеры сценариев Azure PowerShell](../samples-cli.md).
