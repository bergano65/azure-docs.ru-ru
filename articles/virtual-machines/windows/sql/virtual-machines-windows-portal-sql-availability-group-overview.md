---
title: Обзор групп доступности SQL Server и виртуальных машин Azure | Документация Майкрософт
description: В этой статье рассматриваются группы доступности SQL Server на виртуальных машинах Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
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
ms.openlocfilehash: b9977965dc076ec36aa90680a1732b6640b1e41a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60325823"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Введение в группы доступности AlwaysOn SQL Server на виртуальных машинах Azure #

В этой статье рассматриваются группы доступности SQL Server на виртуальных машинах Azure. 

Группы доступности AlwaysOn на виртуальных машинах Azure аналогичны группам доступности AlwaysOn в локальной среде. Дополнительные сведения см. в разделе [Группы доступности AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

На схеме показаны компоненты полной группы доступности SQL Server на виртуальных машинах Azure.

![Группа доступности](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Основное отличие группы доступности на виртуальных машинах Azure состоит в том, что для виртуальных машин Azure требуется [подсистема балансировки нагрузки](../../../load-balancer/load-balancer-overview.md). Подсистема балансировки нагрузки хранит IP-адреса для прослушивателя группы доступности. Если используется несколько групп доступности, то каждой из них требуется прослушиватель. Одна подсистема балансировки нагрузки может обслуживать несколько прослушивателей.

Кроме того на отказоустойчивый кластер гостевых виртуальных Машин Azure IaaS рекомендуется одним сетевым Адаптером на каждом сервере (узел кластера) и одной подсети. Сеть Azure обладает физической избыточностью, что делает ненужными дополнительные сетевые адаптеры и подсети в кластере гостевых виртуальных машин IaaS Azure. Несмотря на то, что отчет о проверке кластера выдаст предупреждение о том, что узлы доступны только в одной сети, это предупреждение можно спокойно проигнорировать в отказоустойчивых кластерах гостевых виртуальных машин IaaS Azure. 

|  | Версия Windows Server | Версия SQL Server | Выпуск SQL Server | Конфигурация кворума WSFC | Аварийное Восстановление с поддержкой нескольких регионов | Поддержка нескольких подсетей | Поддержка существующих Рекламы | Аварийное Восстановление с многозонных том же регионе | Поддержка dist-AG без домена AD | Поддержка dist-AG с без кластера |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ ВИРТУАЛЬНОЙ МАШИНЫ SQL](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | ENT | Облако-свидетель | Нет | Да | Да | Да | Нет | Нет |
| [Шаблоны быстрого запуска](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | ENT | Облако-свидетель | Нет | Да | Да | Да | Нет | Нет |
| [Шаблон портала](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | ENT | Общая папка | Нет | Нет | Нет | Нет | Нет | Нет |
| [Вручную](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Все | Все | Все | Все | Да | Да | Да | Да | Да | Да |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Когда все будет готово к созданию группы доступности SQL Server на виртуальных машинах Azure, обратитесь к этим руководствам.

## <a name="manually-with-azure-cli"></a>Вручную с помощью Azure CLI
С помощью Azure CLI для настройки и развертывания группы доступности рекомендуется, так как это лучшее с точки зрения простоты и скорость развертывания. С помощью интерфейса командной строки Azure, Создание отказоустойчивого кластера Windows, присоединение виртуальных машин SQL Server в кластер, а также создание прослушивателя и внутренней подсистемы балансировки нагрузки могут быть осуществлены в течение 30 минут. Этот параметр по-прежнему требует ручного создания группы доступности, но автоматизирует все другие необходимые этапы настройки. 

Дополнительные сведения см. в разделе [использовании CLI виртуальной Машины SQL Azure для настройки группы доступности AlwaysOn для SQL Server на виртуальной Машине Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Автоматически с помощью шаблонов быстрого запуска Azure
Шаблоны быстрого запуска Azure используют поставщик ресурсов виртуальной Машины SQL должен развернуть отказоустойчивый кластер Windows, присоединить к ней виртуальных машин SQL Server, создайте прослушиватель и внутренний балансировщик нагрузки. Этот параметр по-прежнему требуется вручную создать группу доступности и внутренний балансировщик нагрузки (ILB), но автоматизирует и упрощает все другие необходимые действия по настройке (включая конфигурацию внутреннего балансировщика нагрузки). 

Дополнительные сведения см. в разделе [использовать шаблон быстрого запуска Azure для настройки группы доступности AlwaysOn для SQL Server на виртуальной Машине Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Автоматически с помощью шаблона портала Azure

[Автоматическая настройка группы доступности AlwaysOn на виртуальной машине Azure в модели Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Вручную на портале Azure

Имеется возможность создать виртуальные машины самостоятельно, без шаблона. Сначала выполните необходимые условия, после чего создайте группу доступности. Ознакомьтесь со следующими разделами. 

- [Настройка необходимых компонентов для создания групп доступности AlwaysOn в виртуальных машинах Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Создание группы доступности AlwaysOn для улучшения доступности и аварийного восстановления](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Дальнейшие действия

[Настройка группы доступности AlwaysOn на виртуальных машинах Azure в разных регионах](virtual-machines-windows-portal-sql-availability-group-dr.md)
