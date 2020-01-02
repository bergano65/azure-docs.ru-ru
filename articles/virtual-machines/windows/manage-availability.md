---
title: Управление доступностью виртуальных машин Windows в Azure
description: Описывается, как использовать несколько виртуальных машин для обеспечения высокой доступности приложения Windows в Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1f9914b84b63f271c7dd7d1b8f7dbc3b69511605
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561193"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Управление доступностью виртуальных машин Windows в Azure 

Изучите способы настройки нескольких виртуальных машин и управления ими для обеспечения высокой доступности приложения Windows в Azure. Вы также можете [управлять доступностью виртуальных машин Linux](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Инструкции по созданию и использованию групп доступности в классической модели развертывания см. в разделе [Как настроить группу доступности для виртуальных машин в классической модели развертывания](classic/configure-availability-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Дальнейшие действия
Чтобы больше узнать о балансировке нагрузки виртуальных машин, ознакомьтесь с разделом [Балансировка нагрузки виртуальных машин](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Просмотр эталонных архитектур для запуска N-уровневого приложения на SQL Server в IaaS

* [N-уровневое приложение Windows в Azure с SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Запуск N-уровневого приложения в нескольких регионах Azure для обеспечения высокой доступности](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
