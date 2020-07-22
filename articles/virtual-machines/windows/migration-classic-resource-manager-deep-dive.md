---
title: Технические сведения о переходе с классической модели на Azure Resource Manager
description: Техническое подробное рассмотрение поддерживаемых платформой миграции ресурсов из классической модели развертывания в Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 5ef2085f41cca71e835e05bce97f787ad50a7a53
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508514"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager

> [!IMPORTANT]
> Сегодня около 90% виртуальных машин IaaS используют [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). По состоянию на 28 февраля 2020, классические виртуальные машины являются устаревшими и будут полностью сняты с 1 марта 2023. [Узнайте больше]( https://aka.ms/classicvmretirement) об этой нерекомендуемости и [о том, как она влияет на вас](../classic-vm-deprecation.md#how-does-this-affect-me).

Давайте подробно рассмотрим миграцию из классической модели развертывания Azure в модель развертывания с помощью Azure Resource Manager. Мы рассмотрим ресурсы на уровне ресурсов и функций. Это поможет вам понять, как платформа Azure переносит ресурсы между двумя моделями развертывания. Чтобы получить дополнительную информацию, прочтите статью с объявлением о выпуске службы [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор поддерживаемого платформой переноса ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Планирование переноса ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью интерфейса командной строки Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Перенос VPN-шлюза из классической модели развертывания в модель Resource Manager](../../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [Перенос связанных виртуальных сетей ExpressRoute из классической модели на модель Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md)
* [Community tools to migrate IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Инструменты сообщества для перемещения ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Распространенные ошибки миграции](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Часто задаваемые вопросы о миграции из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
