---
title: Пример сценария для интерфейса командной строки Service Fabric. Вывод списка приложений в кластере
description: Пример сценария для интерфейса командной строки Service Fabric для вывода списка приложений, подготовленных в кластере Service Fabric.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/13/2018
ms.author: aljo
ms.custom: ''
ms.openlocfilehash: 60cb5aaf93521a3e2f7c922949d112ad96a00180
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805643"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Вывод списка приложений, работающих в кластере Service Fabric

Этот пример скрипта подключается к кластеру Service Fabric и выводит список всех подготовленных приложений.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в [документации интерфейса командной строки Service Fabric](../service-fabric-cli.md).

Дополнительные примеры сценариев интерфейса командной строки Service Fabric для Azure Service Fabric см. в статье [Примеры сценариев Azure PowerShell](../samples-cli.md).
