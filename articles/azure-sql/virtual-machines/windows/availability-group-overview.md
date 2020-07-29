---
title: Общие сведения о SQL Server группы доступности Always On
description: В этой статье содержатся сведения о SQL Server группы доступности Always On на виртуальных машинах Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 0e676e1c97c0039490137aa12b06fc88e457646f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286814"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Введение группы доступности Always On SQL Server на виртуальных машинах Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

В этой статье рассматриваются группы доступности SQL Server на виртуальных машинах Azure. 

Группы доступности AlwaysOn на виртуальных машинах Azure аналогичны группам доступности AlwaysOn в локальной среде. Дополнительные сведения см. в разделе [Группы доступности AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

На следующей схеме показаны части полной группы доступности SQL Server на виртуальных машинах Azure.

![Группа доступности](./media/availability-group-overview/00-EndstateSampleNoELB.png)

Основное отличие группы доступности на виртуальных машинах Azure заключается в том, что для этих виртуальных машин требуется [балансировщик нагрузки](../../../load-balancer/load-balancer-overview.md). Подсистема балансировки нагрузки хранит IP-адреса для прослушивателя группы доступности. При наличии нескольких групп доступности для каждой группы требуется прослушиватель. Одна подсистема балансировки нагрузки может обслуживать несколько прослушивателей.

Кроме того, для отказоустойчивого кластера гостевой виртуальной машины IaaS Azure рекомендуется использовать одну сетевую карту на сервер (узел кластера) и одну подсеть. Сеть Azure обладает физической избыточностью, что делает ненужными дополнительные сетевые адаптеры и подсети в кластере гостевых виртуальных машин IaaS Azure. Несмотря на то, что отчет о проверке кластера выдаст предупреждение о том, что узлы доступны только в одной сети, это предупреждение можно спокойно проигнорировать в отказоустойчивых кластерах гостевых виртуальных машин IaaS Azure. 

Для повышения избыточности и обеспечения высокого уровня доступности виртуальные машины SQL Server должны находиться в одной [группе доступности](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets) или в разных [зонах доступности](/azure/availability-zones/az-overview). 

|  | Версия Windows Server | Версия SQL Server | Выпуск SQL Server | Конфигурация кворума WSFC | Аварийное восстановление с несколькими регионами | Поддержка нескольких подсетей | Поддержка существующего каталога AD | Аварийное восстановление с несколькими зонами в одном регионе | Поддержка Dist-AG без домена AD | Поддержка Dist-AG без кластера |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| **[CLI для виртуальных машин SQL](availability-group-az-cli-configure.md)** | 2016 | 2017 </br>2016   | Enterprise | Облако-свидетель | Нет | Да | Да | Да | Нет | Нет |
| **[Шаблоны быстрого запуска](availability-group-quickstart-template-configure.md)** | 2016 | 2017</br>2016  | Enterprise | Облако-свидетель | Нет | Да | Да | Да | Нет | Нет |
| **[Вручную](availability-group-manually-configure-prerequisites-tutorial.md)** | Все | All | All | All | Да | да | да | да | да | да |

Шаблон **кластера SQL Server AlwaysOn (Предварительная версия)** удален из Azure Marketplace и более недоступен. 

Когда все будет готово к созданию группы доступности SQL Server на виртуальных машинах Azure, обратитесь к этим руководствам.

## <a name="manually-with-azure-cli"></a>Вручную с помощью Azure CLI

Для настройки и развертывания группы доступности рекомендуется использовать Azure CLI, так как это самый простой и быстрый способ развертывания. При работе с Azure CLI создание отказоустойчивого кластера Windows, присоединение виртуальных машин SQL Server к этому кластеру, создание прослушивателя и внутренних подсистем балансировки нагрузки можно выполнить меньше чем за 30 минут. Этот вариант по-прежнему требует создания группы доступности вручную, но автоматизирует все остальные необходимые действия по настройке. 

Дополнительные сведения см. в статье [Настройка группы доступности Always On для SQL Server на виртуальной машине Azure с помощью Azure CLI](availability-group-az-cli-configure.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Автоматическое выполнение с помощью шаблонов быстрого запуска Azure

В шаблонах быстрого запуска Azure используются ресурсы виртуальных машин SQL для создания отказоустойчивого кластера Windows, присоединения к нему виртуальных машин SQL Server, создания прослушивателя и настройки внутренней подсистемы балансировки нагрузки. Этот вариант по-прежнему требует ручного создания группы доступности и внутренней Load Balancer (ILB). Однако она автоматизирует и упрощает все остальные необходимые действия по настройке, включая настройку ILB. 

Дополнительные сведения см. в статье [Настройка группы доступности Always On для SQL Server на виртуальной машине Azure с помощью шаблона быстрого запуска Azure ](availability-group-quickstart-template-configure.md).


## <a name="automatically-with-an-azure-portal-template"></a>Автоматически с помощью шаблона портала Azure

[Автоматическая настройка группы доступности AlwaysOn на виртуальной машине Azure в модели Resource Manager](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-the-azure-portal"></a>Вручную в портал Azure

Имеется возможность создать виртуальные машины самостоятельно, без шаблона. Сначала выполните необходимые условия, после чего создайте группу доступности. См. следующие статьи: 

- [Настройка необходимых компонентов для создания групп доступности AlwaysOn в виртуальных машинах Azure](availability-group-manually-configure-prerequisites-tutorial.md)

- [Создание группы доступности AlwaysOn для улучшения доступности и аварийного восстановления](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>Дальнейшие действия

[Настройка группы доступности AlwaysOn на виртуальных машинах Azure в разных регионах](availability-group-manually-configure-multiple-regions.md)
