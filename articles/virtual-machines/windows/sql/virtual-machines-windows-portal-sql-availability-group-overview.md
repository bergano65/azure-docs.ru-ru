---
title: Обзор групп доступности
description: В этой статье рассматриваются группы доступности SQL Server на виртуальных машинах Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8119990ab4ab4a918e325976092100086a547aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037496"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Представляем группы доступности серверов S'L на виртуальных машинах Azure

В этой статье рассматриваются группы доступности SQL Server на виртуальных машинах Azure. 

Группы доступности AlwaysOn на виртуальных машинах Azure аналогичны группам доступности AlwaysOn в локальной среде. Дополнительные сведения см. в разделе [Группы доступности AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

На схеме показаны компоненты полной группы доступности SQL Server на виртуальных машинах Azure.

![Группа доступности](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Основное отличие группы доступности на виртуальных машинах Azure состоит в том, что для виртуальных машин Azure требуется [подсистема балансировки нагрузки](../../../load-balancer/load-balancer-overview.md). Подсистема балансировки нагрузки хранит IP-адреса для прослушивателя группы доступности. Если используется несколько групп доступности, то каждой из них требуется прослушиватель. Одна подсистема балансировки нагрузки может обслуживать несколько прослушивателей.

Кроме того, в кластере сбоя схватом варианте Azure IaaS VM мы рекомендуем один NIC на сервер (кластерный узел) и одну подсеть. Сеть Azure обладает физической избыточностью, что делает ненужными дополнительные сетевые адаптеры и подсети в кластере гостевых виртуальных машин IaaS Azure. Несмотря на то, что отчет о проверке кластера выдаст предупреждение о том, что узлы доступны только в одной сети, это предупреждение можно спокойно проигнорировать в отказоустойчивых кластерах гостевых виртуальных машин IaaS Azure. 

Для увеличения избыточности и высокой доступности, ВМ сервера S'L должны быть либо в [одном наборе доступности,](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)либо в [различных зонах доступности.](/azure/availability-zones/az-overview) 

|  | Версия windows Server | Версия SQL Server | Выпуск SQL Server | Кворум Кворум | DR с мультирегиона | Поддержка мультиподсетей | Поддержка существующего AD | DR с многозонным регионом | Поддержка Dist-AG без доменa AD | Поддержка Dist-AG без кластера |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [СЗЛ VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Лор | Облако-свидетель | нет | Да | Да | Да | нет | нет |
| [Шаблоны быстрого запуска](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Лор | Облако-свидетель | нет | Да | Да | Да | нет | нет |
| [Шаблон портала](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Лор | Общая папка | нет | нет | нет | нет | нет | нет |
| [Вручную](virtual-machines-windows-portal-sql-availability-group-prereq.md) | All | All | All | All | Да | Да | Да | Да | Да | Да |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Когда все будет готово к созданию группы доступности SQL Server на виртуальных машинах Azure, обратитесь к этим руководствам.

## <a name="manually-with-azure-cli"></a>Вручную с Azure CLI
Использование Azure CLI для настройки и развертывания группы доступности является рекомендуемым вариантом, так как он является лучшим с точки зрения простоты и скорости развертывания. С Помощью Azure CLI можно добиться создания кластера Windows Failover, присоединения к кластеру VMs-серверов, а также создания слушателя и баланса внутренней нагрузки менее чем за 30 минут. Эта опция по-прежнему требует ручного создания группы доступности, но автоматизирует все другие необходимые шаги конфигурации. 

Для получения дополнительной информации [см.](virtual-machines-windows-sql-availability-group-cli.md) 

## <a name="automatically-with-azure-quickstart-templates"></a>Автоматически с помощью шаблонов Azure кикстарта
Шаблоны Azure quickstart используют поставщика ресурсов S'L VM для развертывания кластера Windows Failover, присоединения к вам vMs-сервера, создания слушателя и настройки баланса внутренней нагрузки. Эта опция по-прежнему требует ручного создания группы доступности и баланса внутренней нагрузки (ILB), но автоматизирует и упрощает все другие необходимые шаги конфигурации (включая конфигурацию ILB). 

Для получения дополнительной информации [см.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


## <a name="automatically-with-an-azure-portal-template"></a>Автоматически с шаблоном портала Azure

[Автоматическая настройка группы доступности AlwaysOn на виртуальной машине Azure в модели Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Вручную на портале Azure

Имеется возможность создать виртуальные машины самостоятельно, без шаблона. Сначала выполните необходимые условия, после чего создайте группу доступности. См. следующие статьи: 

- [Настройка необходимых компонентов для создания групп доступности AlwaysOn в виртуальных машинах Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Создание группы доступности AlwaysOn для улучшения доступности и аварийного восстановления](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Дальнейшие действия

[Наймите сервер S'L Всегда на группе доступности на виртуальных машинах Azure в разных регионах](virtual-machines-windows-portal-sql-availability-group-dr.md)
