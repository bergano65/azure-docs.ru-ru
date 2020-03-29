---
title: Классический для менеджера ресурсов Azure техническое глубокое погружение
description: Техническое глубокое погружение в миграцию ресурсов, поддерживаемых платформой, от классической модели развертывания к менеджеру ресурсов Azure.
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 1ee40d32-a5e8-42a2-97d0-3232fd3cbb98
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f3b4a601fe65c4227a5d876fe4db16f253ca160b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77915475"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager

> [!IMPORTANT]
> Сегодня около 90% IaaS VMs используют [azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) По состоянию на 28 февраля 2020 года классические ВМ были обесточены и будут полностью выведены из эксплуатации 1 марта 2023 года. [Узнайте больше]( https://aka.ms/classicvmretirement) об этом амортизме и [как он влияет на вас.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Давайте подробно рассмотрим миграцию из классической модели развертывания Azure в модель развертывания с помощью Azure Resource Manager. Мы рассмотрим ресурсы на уровне ресурсов и функций. Это поможет вам понять, как платформа Azure переносит ресурсы между двумя моделями развертывания. Чтобы получить дополнительную информацию, прочтите статью с объявлением о выпуске службы [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор поддерживаемого платформой переноса ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Планирование переноса ресурсов IaaS из классической модели развертывания в модель Azure Resource Manager.](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью интерфейса командной строки Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Перенос VPN-шлюза из классической модели развертывания в модель Resource Manager](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [Перенос связанных виртуальных сетей ExpressRoute из классической модели на модель Resource Manager](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Community tools to migrate IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Инструменты сообщества для перемещения ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Распространенные ошибки миграции](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Часто задаваемые вопросы о миграции из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
