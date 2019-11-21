---
title: Azure Resource Manager support for Load Balancer
description: In this article, use Azure PowerShell and templates with Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 57a60a65dee995695224aa1b451e848ea8768ab1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215391"
---
# <a name="azure-resource-manager-support-with-azure-load-balancer"></a>Azure Resource Manager support with Azure Load Balancer



Azure Resource Manager представляет собой предпочтительную платформу управления для служб в Azure. Теперь Azure Load Balancer можно управлять с помощью интерфейсов API и инструментов на основе Azure Resource Manager.

## <a name="concepts"></a>Концепции

Azure Load Balancer с Resource Manager содержит следующие дочерние ресурсы.

* Front-end IP configuration – a load balancer can include one or more frontend IP addresses, otherwise known as a virtual IPs (VIPs). Такие IP-адреса обеспечивают поступление трафика.
* Back-end address pool – This pool is a collection of IP addresses associated with the virtual machine Network Interface Card (NIC) to which load is distributed.
* Load-balancing rules – a rule property maps a given frontend IP and port combination to a set of back-end IP addresses and port combination. A single load balancer can have multiple load-balancing rules. Each rule is a combination of a frontend IP and port and back-end IP and port associated with VMs.
* Пробы: пробы позволяют отслеживать работоспособность экземпляров виртуальных машин. В случае сбоя пробы работоспособности экземпляр виртуальной машины автоматически перестает использоваться.
* Inbound NAT rules – NAT rules defining the inbound traffic flowing through the frontend IP and distributed to the back-end IP.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Шаблоны быстрого запуска

Azure Resource Manager позволяет предоставлять ваши приложения при помощи декларативного шаблона. В одном шаблоне можно развернуть несколько служб наряду с компонентами, от которых зависит их работа. Вы используете один и тот же шаблон для многократного развертывания вашего приложения на каждой стадии его жизненного цикла.

Templates may include definitions for:
* **Виртуальные машины**
* **Виртуальные сети**
* **Группы доступности**
* **Network interfaces (NICs)**
* **Учетные записи хранения**
* **Подсистемы балансировки нагрузки**
* **Группы безопасности сети**
* **Public IPs.** 

С помощью шаблонов можно создать все необходимое для сложного приложения. Файл шаблона можно вернуть в систему управления содержимым, чтобы обеспечить управление версиями и возможность совместной работы.

[Дополнительные сведения о шаблонах](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Дополнительные сведения о сетевых ресурсах](../networking/networking-overview.md)

For Quickstart templates using Azure Load Balancer, see the [GitHub repository](https://github.com/Azure/azure-quickstart-templates) that hosts a set of community-generated templates.

Примеры шаблонов:

* [2 ВМ в подсистеме балансировки нагрузки и правила балансировки нагрузки](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 VMs in a VNET with an Internal Load Balancer and load balancer rules](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 VMs in a load balancer and configure NAT rules on the LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Настройка балансировки нагрузки Azure с помощью PowerShell или интерфейса командной строки

Get started with Azure Resource Manager cmdlets, command-line tools, and REST APIs

* [Сетевые командлеты Azure](https://docs.microsoft.com/powershell/module/az.network#networking) можно использовать для создания подсистемы балансировки нагрузки.
* [Создание подсистемы балансировки нагрузки с помощью диспетчера ресурсов Azure](load-balancer-get-started-ilb-arm-ps.md)
* [Использование Azure CLI с диспетчером ресурсов Azure (ARM)](../xplat-cli-azure-resource-manager.md)
* [Интерфейсы API REST подсистемы балансировки нагрузки](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Дальнейшие действия

[Get started creating an Internet facing load balancer](load-balancer-get-started-internet-arm-ps.md) and configure the type of [distribution mode](load-balancer-distribution-mode.md) for specific network traffic behavior.

Узнайте, как управлять [временем ожидания простоя TCP для балансировщика нагрузки](load-balancer-tcp-idle-timeout.md). These settings are important when your application needs to keep connections alive for servers behind a load balancer.
