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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037496"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Введение SQL Server групп доступности на виртуальных машинах Azure

В этой статье рассматриваются группы доступности SQL Server на виртуальных машинах Azure. 

Группы доступности AlwaysOn на виртуальных машинах Azure аналогичны группам доступности AlwaysOn в локальной среде. Дополнительные сведения см. в разделе [Группы доступности AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

На схеме показаны компоненты полной группы доступности SQL Server на виртуальных машинах Azure.

![Группа доступности](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Основное отличие группы доступности на виртуальных машинах Azure состоит в том, что для виртуальных машин Azure требуется [подсистема балансировки нагрузки](../../../load-balancer/load-balancer-overview.md). Подсистема балансировки нагрузки хранит IP-адреса для прослушивателя группы доступности. Если используется несколько групп доступности, то каждой из них требуется прослушиватель. Одна подсистема балансировки нагрузки может обслуживать несколько прослушивателей.

Кроме того, в гостевом отказоустойчивом кластере виртуальной машины Azure IaaS рекомендуется одна сетевая карта на сервер (узел кластера) и одна подсеть. Сеть Azure обладает физической избыточностью, что делает ненужными дополнительные сетевые адаптеры и подсети в кластере гостевых виртуальных машин IaaS Azure. Несмотря на то, что отчет о проверке кластера выдаст предупреждение о том, что узлы доступны только в одной сети, это предупреждение можно спокойно проигнорировать в отказоустойчивых кластерах гостевых виртуальных машин IaaS Azure. 

Для повышения избыточности и высокой доступности SQL Server виртуальные машины должны находиться в одной [группе доступности](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)или в разных [зонах доступности](/azure/availability-zones/az-overview). 

|  | Версия Windows Server | Версия SQL Server | Выпуск SQL Server | Конфигурация кворума WSFC | Аварийное восстановление с несколькими регионами | Поддержка нескольких подсетей | Поддержка существующего AD | Аварийное восстановление с несколькими зонами в разных регионах | Расп — поддержка группы доступности без домена AD | Расп — поддержка группы доступности без кластера |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ SQL VM](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | ENT | Облако-свидетель | Нет | Yes | Yes | Yes | Нет | Нет |
| [Шаблоны быстрого запуска](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | ENT | Облако-свидетель | Нет | Yes | Yes | Yes | Нет | Нет |
| [Шаблон портала](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | ENT | Общая папка | Нет | Нет | Нет | Нет | Нет | Нет |
| [Вручную](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Все | Все | Все | Все | Yes | Yes | Yes | Yes | Yes | Yes |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Когда все будет готово к созданию группы доступности SQL Server на виртуальных машинах Azure, обратитесь к этим руководствам.

## <a name="manually-with-azure-cli"></a>Вручную с Azure CLI
Использование Azure CLI для настройки и развертывания группы доступности является рекомендуемым вариантом, так как он является лучшим в плане простоты и скорости развертывания. С Azure CLI, создание отказоустойчивого кластера Windows, присоединение виртуальных машин SQL Server к кластеру, а также создание прослушивателя и внутренних Load Balancer могут быть выполнены в течение 30 минут. Этот вариант по-прежнему требует создания группы доступности вручную, но автоматизирует все остальные необходимые действия по настройке. 

Дополнительные сведения см. [в статье Использование интерфейса командной строки Azure SQL VM для настройки Always on группы доступности для SQL Server на виртуальной машине Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Автоматическое Руководство по шаблонам быстрого запуска Azure
Шаблоны быстрого запуска Azure используют поставщик ресурсов виртуальной машины SQL для развертывания отказоустойчивого кластера Windows, присоединение к нему виртуальных машин SQL Server, создание прослушивателя и Настройка внутренних Load Balancer. Этот вариант по-прежнему требует создания группы доступности вручную, а внутренняя Load Balancer (ILB), но автоматизирует и упрощает все остальные необходимые действия по настройке (включая настройку ILB). 

Дополнительные сведения см. [в статье Использование шаблона быстрого запуска Azure для настройки Always on группы доступности для SQL Server на виртуальной машине Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Автоматически с помощью шаблона портал Azure

[Автоматическая настройка группы доступности AlwaysOn на виртуальной машине Azure в модели Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Вручную в портал Azure

Имеется возможность создать виртуальные машины самостоятельно, без шаблона. Сначала выполните необходимые условия, после чего создайте группу доступности. Ознакомьтесь со следующими разделами. 

- [Настройка необходимых компонентов для создания групп доступности AlwaysOn в виртуальных машинах Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Создание группы доступности AlwaysOn для улучшения доступности и аварийного восстановления](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Дополнительная информация

[Настройка группы доступности AlwaysOn на виртуальных машинах Azure в разных регионах](virtual-machines-windows-portal-sql-availability-group-dr.md)
