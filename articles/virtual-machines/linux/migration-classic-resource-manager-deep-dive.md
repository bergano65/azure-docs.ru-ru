---
title: Техническое руководство по поддерживаемой платформой миграции из классической модели в модель Azure Resource Manager| Документация Майкрософт
description: Эта статья является техническим руководством по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 29267453-f894-4180-bb67-dce2a0e062bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: cae0a8ebcaae241554cfd1e4affddf7e905e4621
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125371"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Техническое руководство по поддерживаемому платформой переносу из классической модели в модель развертывания с помощью Azure Resource Manager

Давайте подробно рассмотрим миграцию из классической модели развертывания Azure в модель развертывания с помощью Azure Resource Manager. Мы рассмотрим ресурсы на уровне ресурсов и функций. Это поможет вам понять, как платформа Azure переносит ресурсы между двумя моделями развертывания. Дополнительные сведения см. в статье объявления службы: [Поддерживаемый платформой перенос ресурсов IaaS из классической модели развертывания с помощью Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор поддерживаемого платформой переноса ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Планирование переноса ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью интерфейса командной строки Azure](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Community tools to migrate IaaS resources from classic to Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Инструменты сообщества для перемещения ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Распространенные ошибки миграции](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Часто задаваемые вопросы о миграции из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
