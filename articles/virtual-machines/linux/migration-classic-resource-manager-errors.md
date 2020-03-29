---
title: Распространенные ошибки при миграции из классической модели в модель Azure Resource Manager
description: В этой статье перечислены самые распространенные ошибки при переносе ресурсов IaaS из управления службами Azure в стек Azure Resource Manager, а также способы их устранения.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: ffeef74179874a0f477ba857ca519ebc67fded64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944711"
---
# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>Распространенные ошибки при миграции из классической модели в модель Azure Resource Manager

> [!IMPORTANT]
> Сегодня около 90% IaaS VMs используют [azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) По состоянию на 28 февраля 2020 года классические ВМ были обесточены и будут полностью выведены из эксплуатации 1 марта 2023 года. [Узнайте больше]( https://aka.ms/classicvmretirement) об этом амортизме и [как он влияет на вас.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

[!INCLUDE [virtual-machines-common-migration-errors](../../../includes/virtual-machines-common-classic-resource-manager-migration-common-errors.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор поддерживаемого платформой переноса ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Планирование переноса ресурсов IaaS из классической модели развертывания в модель Azure Resource Manager.](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью интерфейса командной строки Azure](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Community tools to migrate IaaS resources from classic to Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Инструменты сообщества для перемещения ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Часто задаваемые вопросы о миграции из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
