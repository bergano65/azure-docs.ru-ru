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
ms.openlocfilehash: 9528fa3b434a0bc09d191fe6808214feb8245ae4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509454"
---
> [!NOTE]
> Центр безопасности Azure в настоящее время не может выполнять [сканирование уязвимостей образов](../articles/security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) в реестре, который разрешает доступ к частным конечным точкам, выбранным подсетям или IP-адресам. Экземпляры некоторых служб Azure, включая Azure DevOps Services и экземпляры контейнеров Azure, также не могут получить доступ к реестру контейнеров, ограниченному сетью.