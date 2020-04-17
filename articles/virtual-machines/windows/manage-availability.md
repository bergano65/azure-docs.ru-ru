---
title: Управление доступностью вывмов Windows в Azure
description: Описывается, как использовать несколько виртуальных машин для обеспечения высокой доступности приложения Windows в Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 02dec02664cc82392ece49d1c5bd08834c3b9af3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451777"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Управление доступностью виртуальных машин Windows в Azure 

Изучите способы настройки нескольких виртуальных машин и управления ими для обеспечения высокой доступности приложения Windows в Azure. Вы также можете [управлять доступностью виртуальных машин Linux](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Следующие шаги
Чтобы больше узнать о балансировке нагрузки виртуальных машин, ознакомьтесь с разделом [Балансировка нагрузки виртуальных машин](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Просмотр справочных архитектур для запуска n-уровня приложений на сервере S'L в IaaS

* [Использование n-уровневого приложения с SQL Server в Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Запуск n-уровневого приложения в нескольких регионах Azure для обеспечения высокой доступности](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
