---
title: Пример сценария для интерфейса командной строки Service Fabric. Обновление приложения в кластере
description: Пример сценария для интерфейса командной строки Service Fabric для установки новой версии приложения. Этот пример устанавливает новую версию развернутого приложения.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 72195a3a127e33ffa6118f77c4fa58ba5f60ee17
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600088"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Добавление сертификата приложения в кластер Service Fabric

Этот сценарий передает новую версию существующего приложения, а затем обновляет развернутое приложение с ее помощью.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в [документации интерфейса командной строки Service Fabric](../service-fabric-cli.md).

Дополнительные примеры сценариев интерфейса командной строки Service Fabric для Azure Service Fabric см. в статье [Примеры сценариев Azure PowerShell](../samples-cli.md).
