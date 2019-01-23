---
title: Обзор групп доступности SQL Server и виртуальных машин Azure | Документация Майкрософт
description: В этой статье рассматриваются группы доступности SQL Server на виртуальных машинах Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: 5f8ae6d9138a7413b0cca4cca7bcc47c13212674
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358057"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Введение в группы доступности AlwaysOn SQL Server на виртуальных машинах Azure #

В этой статье рассматриваются группы доступности SQL Server на виртуальных машинах Azure. 

Группы доступности AlwaysOn на виртуальных машинах Azure аналогичны группам доступности AlwaysOn в локальной среде. Дополнительные сведения см. в разделе [Группы доступности AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

На схеме показаны компоненты полной группы доступности SQL Server на виртуальных машинах Azure.

![Группа доступности](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Основное отличие группы доступности на виртуальных машинах Azure состоит в том, что для виртуальных машин Azure требуется [подсистема балансировки нагрузки](../../../load-balancer/load-balancer-overview.md). Подсистема балансировки нагрузки хранит IP-адреса для прослушивателя группы доступности. Если используется несколько групп доступности, то каждой из них требуется прослушиватель. Одна подсистема балансировки нагрузки может обслуживать несколько прослушивателей.

Кроме того, для отказоустойчивого кластера гостевой виртуальной машины IaaS Azure рекомендуется использовать один сетевой адаптер на сервер (узел кластера) и одну подсеть. Сеть Azure обладает физической избыточностью, что делает ненужными дополнительные сетевые адаптеры и подсети в кластере гостевых виртуальных машин IaaS Azure. Несмотря на то, что отчет о проверке кластера выдаст предупреждение о том, что узлы доступны только в одной сети, это предупреждение можно спокойно проигнорировать в отказоустойчивых кластерах гостевых виртуальных машин IaaS Azure. 

Когда все будет готово к созданию группы доступности SQL Server на виртуальных машинах Azure, обратитесь к этим руководствам.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Автоматическое создание группы доступности из шаблона

[Автоматическая настройка группы доступности AlwaysOn на виртуальной машине Azure в модели Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Создание группы доступности на портале Azure вручную

Имеется возможность создать виртуальные машины самостоятельно, без шаблона. Сначала выполните необходимые условия, после чего создайте группу доступности. Ознакомьтесь со следующими разделами. 

- [Настройка необходимых компонентов для создания групп доступности AlwaysOn в виртуальных машинах Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Создание группы доступности AlwaysOn для улучшения доступности и аварийного восстановления](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Дополнительная информация

[Настройка группы доступности AlwaysOn на виртуальных машинах Azure в разных регионах](virtual-machines-windows-portal-sql-availability-group-dr.md).
