---
title: Средства сообщества. Перенос классических ресурсов в развертывание с помощью Azure Resource Manager | Документы Microsoft
description: В этой статье перечислены инструменты, которые были предоставлены сообществом для упрощения переноса ресурсов IaaS из классической модели развертывания в модель развертывания с помощью Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 085a0ff0eee260069d693b339521a9489df78e40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848238"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Инструменты сообщества для переноса ресурсов IaaS из классической модели в модель Azure Resource Manager
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
Инструмент migAz — это еще один способ перенести полный набор классических ресурсов IaaS в ресурсы IaaS Azure Resource Manager. Такой перенос может выполняться в той же подписке или между разными подписками и типами подписок (например: Подписки CSP).

[Ссылка на документацию по инструменту](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Следующие шаги

* [Обзор поддерживаемого платформой переноса ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Планирование переноса ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью интерфейса командной строки Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Распространенные ошибки миграции](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Часто задаваемые вопросы о миграции из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

