---
title: Обзор групп доступности Always On SQL Server
description: В этой статье описаны группы доступности Always On SQL Server в Виртуальных машинах Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mathoma
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 34d76d7c85a478b5e31a692e653752aa1653884c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91293668"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Введение в группы доступности Always On SQL Server в Виртуальных машинах Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

В этой статье рассматриваются группы доступности SQL Server на виртуальных машинах Azure. 

Группы доступности AlwaysOn на виртуальных машинах Azure аналогичны группам доступности AlwaysOn в локальной среде. Дополнительные сведения см. в разделе [Группы доступности AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

На следующей схеме показаны компоненты полной группы доступности SQL Server в Виртуальных машинах Azure.

![Группа доступности](./media/availability-group-overview/00-EndstateSampleNoELB.png)

Основное отличие группы доступности в Виртуальных машинах Azure состоит в том, что для виртуальных машин Azure требуется [подсистема балансировки нагрузки](../../../load-balancer/load-balancer-overview.md). Подсистема балансировки нагрузки хранит IP-адреса для прослушивателя группы доступности. Если используется несколько групп доступности, для каждой из них требуется прослушиватель. Одна подсистема балансировки нагрузки может обслуживать несколько прослушивателей.

Кроме того, для отказоустойчивого кластера гостевой виртуальной машины IaaS Azure рекомендуется использовать одну сетевую карту на сервер (узел кластера) и одну подсеть. Сеть Azure обладает физической избыточностью, что делает ненужными дополнительные сетевые адаптеры и подсети в кластере гостевых виртуальных машин IaaS Azure. Несмотря на то, что отчет о проверке кластера выдаст предупреждение о том, что узлы доступны только в одной сети, это предупреждение можно спокойно проигнорировать в отказоустойчивых кластерах гостевых виртуальных машин IaaS Azure. 

Для повышения избыточности и обеспечения высокого уровня доступности виртуальные машины SQL Server должны находиться в одной [группе доступности](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets) или в разных [зонах доступности](/azure/availability-zones/az-overview). 

|  | Версия Windows Server | Версия SQL Server | Выпуск SQL Server | Конфигурация кворума WSFC | Аварийное восстановление с несколькими регионами | Поддержка нескольких подсетей | Поддержка существующего каталога AD | Аварийное восстановление с несколькими зонами в одном регионе | Поддержка Dist-AG без домена AD | Поддержка Dist-AG без кластера |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| **[Портал Azure](availability-group-azure-portal-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016   | Enterprise | Облако-свидетель | Нет | Да | Да | Да | Нет | Нет |
| **[Azure CLI / PowerShell](availability-group-az-cli-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016   | Enterprise | Облако-свидетель | Нет | Да | Да | Да | Нет | Нет |
| **[Шаблоны быстрого запуска](availability-group-quickstart-template-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016  | Enterprise | Облако-свидетель | Нет | Да | Да | Да | Нет | Нет |
| **[Вручную](availability-group-manually-configure-prerequisites-tutorial.md)** | Все | Все | Все | All | Да | Да | Да | Да | Да | Да |

Шаблон **Кластер Always On SQL Server (предварительная версия**) был удален из Azure Marketplace и более недоступен. 

Когда все будет готово к созданию группы доступности SQL Server на виртуальных машинах Azure, обратитесь к этим руководствам.

## <a name="manually-with-azure-cli"></a>Вручную с помощью Azure CLI

Для настройки и развертывания группы доступности рекомендуется использовать Azure CLI, так как это самый простой и быстрый способ развертывания. При работе с Azure CLI создание отказоустойчивого кластера Windows, присоединение виртуальных машин SQL Server к этому кластеру, создание прослушивателя и внутренних подсистем балансировки нагрузки можно выполнить меньше чем за 30 минут. Для этого варианта требуется создать группу доступности вручную, но все остальные шаги по настройке будут автоматизированы. 

Дополнительные сведения см. в статье [Настройка группы доступности Always On для SQL Server на виртуальной машине Azure с помощью Azure CLI](availability-group-az-cli-configure.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Автоматическое выполнение с помощью шаблонов быстрого запуска Azure

В шаблонах быстрого запуска Azure используются ресурсы виртуальных машин SQL для создания отказоустойчивого кластера Windows, присоединения к нему виртуальных машин SQL Server, создания прослушивателя и настройки внутренней подсистемы балансировки нагрузки. Для этого варианта требуется создать группу доступности вручную и внутренний Load Balancer (ILB). При этом все остальные шаги по настройке, включая настройку ILB, будут автоматизированы. 

Дополнительные сведения см. в статье [Настройка группы доступности Always On для SQL Server на виртуальной машине Azure с помощью шаблона быстрого запуска Azure ](availability-group-quickstart-template-configure.md).


## <a name="automatically-with-an-azure-portal-template"></a>Автоматически с помощью шаблона портала Azure

[Автоматическая настройка группы доступности AlwaysOn на виртуальной машине Azure в модели Resource Manager](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-the-azure-portal"></a>Вручную на портале Azure

Имеется возможность создать виртуальные машины самостоятельно, без шаблона. Сначала выполните необходимые условия, после чего создайте группу доступности. См. следующие статьи: 

- [Настройка необходимых компонентов для создания групп доступности AlwaysOn в виртуальных машинах Azure](availability-group-manually-configure-prerequisites-tutorial.md)

- [Создание группы доступности AlwaysOn для улучшения доступности и аварийного восстановления](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>Дальнейшие действия

[Настройка группы доступности AlwaysOn на виртуальных машинах Azure в разных регионах](availability-group-manually-configure-multiple-regions.md)
