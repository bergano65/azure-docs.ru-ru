---
title: Управление доступностью виртуальных машин Windows в Azure
description: Описывается, как использовать несколько виртуальных машин для обеспечения высокой доступности приложения Windows в Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 354cd8d291f8ad5c842d33b5ac63b0deb58b7d41
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81870043"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Управление доступностью виртуальных машин Windows в Azure 

Изучите способы настройки нескольких виртуальных машин и управления ими для обеспечения высокой доступности приложения Windows в Azure. Вы также можете [управлять доступностью виртуальных машин Linux](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Дальнейшие шаги
Чтобы больше узнать о балансировке нагрузки виртуальных машин, ознакомьтесь с разделом [Балансировка нагрузки виртуальных машин](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Просмотрите эталонные образцы архитектуры для выполняющихся N-уровневых приложений на SQL Server в IaaS

* [Использование n-уровневого приложения с SQL Server в Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Запуск n-уровневого приложения в нескольких регионах Azure для обеспечения высокой доступности](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
