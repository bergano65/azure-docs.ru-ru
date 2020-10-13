---
title: включить файл
description: включить файл
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 09/17/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: e3234289fe56286cb5ff0791d983dee6ec2aa4d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91545511"
---
> [!NOTE]
> Центр безопасности Azure в настоящее время не может выполнять [сканирование уязвимостей образов](../articles/security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) в реестре, который разрешает доступ к частным конечным точкам, выбранным подсетям или IP-адресам. Экземпляры некоторых служб Azure, включая Azure DevOps Services и экземпляры контейнеров Azure, также не могут получить доступ к реестру контейнеров, ограниченному сетью.