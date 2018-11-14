---
title: Настройка конечных точек в классической виртуальной машине Windows | Документация Майкрософт
description: Узнайте, как настроить конечные точки для классической виртуальной машины Windows на портале Azure, чтобы обеспечить обмен данными с виртуальной машиной Windows в Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: cynthn
ms.openlocfilehash: 373003eb8be0482ed96d7d11ecd879237f69b47a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957171"
---
# <a name="set-up-endpoints-on-a-windows-virtual-machine-by-using-the-classic-deployment-model"></a>Настройка конечных точек на виртуальной машине Windows с помощью классической модели развертывания
Все виртуальные машины Windows, созданные в Azure с помощью классической модели развертывания, могут автоматически взаимодействовать с другими виртуальными машинами в той же облачной службе или виртуальной сети, используя канал частной сети. Однако компьютерам в Интернете или другим виртуальным сетям требуются конечные точки, чтобы направить входящий трафик к виртуальной машине. 

Вы также можете настроить конечные точки на [виртуальных машинах Linux](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается классическая модель развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.  
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

В модели развертывания с помощью **Resource Manager** конечные точки настраиваются с использованием **групп безопасности сети (NSG)**. Дополнительные сведения см. в статье [Открытие портов для виртуальной машины в Azure с помощью портала Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

При создании виртуальной машины Windows на портале Azure обычно автоматически создаются общие конечные точки, такие как конечные точки для удаленного рабочего стола и удаленного взаимодействия Windows PowerShell. Позднее при необходимости вы можете настроить дополнительные конечные точки.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Дополнительная информация
* Инструкции по использованию командлета Azure PowerShell для настройки конечной точки виртуальной машины см. в разделе [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Инструкции по управлению списком управления доступом к конечной точке с помощью командлета Azure PowerShell см. в разделе [Управление списками управления доступом для конечных точек с помощью PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Если вы создали виртуальную машину, используя модель развертывания с помощью Resource Manager, [создайте группы безопасности сети](../../../virtual-network/tutorial-filter-network-traffic.md) с помощью Azure PowerShell и управляйте трафиком к этой виртуальной машине.
