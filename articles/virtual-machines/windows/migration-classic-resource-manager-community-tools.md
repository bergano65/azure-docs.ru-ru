---
title: Средства сообщества — перемещение классических ресурсов в Azure Resource Manager
description: В этой статье перечислены инструменты, которые были предоставлены сообществом для упрощения переноса ресурсов IaaS из классической модели развертывания в модель развертывания с помощью Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f4298af05f021f0a9579a9837308be5d15a3c14f
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915407"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Инструменты сообщества для переноса ресурсов IaaS из классической модели в модель Azure Resource Manager

> [!IMPORTANT]
> Сегодня около 90% виртуальных машин IaaS используют [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). По состоянию на 28 февраля 2020, классические виртуальные машины являются устаревшими и будут полностью сняты с 1 марта 2023. [Узнайте больше]( https://aka.ms/classicvmretirement) об этой нерекомендуемости и [о том, как она влияет на вас](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

В этой статье перечислены инструменты, которые были предоставлены сообществом, чтобы упростить перенос ресурсов IaaS из классической модели развертывания в модель развертывания с помощью Azure Resource Manager.

> [!NOTE]
> Эти инструменты официально не поддерживаются службой поддержки Майкрософт. Они представлены на GitHub с открытым кодом, и мы охотно принимаем запросы на включение внесенных изменений, связанные с исправлениями или дополнительными сценариями. Чтобы сообщить о проблеме, воспользуйтесь вкладкой "Issues" (Проблемы) на сайте GitHub.
> 
> Перенос с использованием этих инструментов вызовет простой классической виртуальной машины. Если вы хотите выполнить поддерживаемый платформой перенос, см. следующие статьи: 
> 
>   * [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Это коллекция вспомогательных инструментов, созданных в рамках корпоративных миграций из модели управления службами Azure в модель Azure Resource Manager. С помощью этого инструмента можно реплицировать инфраструктуру в другую подписку, которая может использоваться для тестирования миграции и устранения любых проблем перед выполнением миграции в рабочей подписке.

[Ссылка на документацию по инструменту](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
Инструмент migAz — это еще один способ перенести полный набор классических ресурсов IaaS в ресурсы IaaS Azure Resource Manager. Такой перенос может выполняться в пределах одной подписки или между разными подписками и типами подписок (например: подписки CSP).

[Ссылка на документацию по инструменту](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Следующие шаги

* [Обзор поддерживаемого платформой переноса ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Планирование переноса ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью интерфейса командной строки Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Распространенные ошибки миграции](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Часто задаваемые вопросы о миграции из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

