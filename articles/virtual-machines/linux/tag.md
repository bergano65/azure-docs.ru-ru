---
title: Пометка виртуальной машины Azure с помощью интерфейса командной строки
description: Сведения о добавлении тегов виртуальной машины с помощью Azure CLI.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1a417e7cff4c7afb601861ddfe09eec171f0cf15
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320619"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Пометка виртуальной машины Linux в Azure
В этой статье описываются разные способы назначения тегов виртуальной машине Linux в Azure с использованием модели развертывания Resource Manager. Теги — это определяемые пользователем пары "ключ-значение", которые можно помещать непосредственно в ресурс или группу ресурсов. В настоящее время Azure поддерживает до 50 тегов на ресурс и группу ресурсов. Теги можно добавлять к ресурсу во время его создания или к уже существующему ресурсу. Обратите внимание, что теги поддерживаются только для тех ресурсов, которые созданы с помощью модели развертывания Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Отметка тегами с помощью интерфейса командной строки Azure

Для начала вам нужно установить последнюю версию [Azure CLI](/cli/azure/install-azure-cli) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az-login).

Вы можете просмотреть все свойства определенной виртуальной машины, включая теги, с помощью следующей команды:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Чтобы добавить новый тег виртуальной машины через Azure CLI, можно применить команду `azure vm update` с параметром тега **--set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Чтобы удалить теги, используйте в команде `azure vm update` параметр **--remove**.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Теперь, когда мы применили теги к ресурсам с помощью Azure CLI и портала, рассмотрим сведения об использовании, чтобы увидеть теги на портале выставления счетов.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о добавлении тегов для ресурсов Azure см. в статьях [Общие сведения об Azure Resource Manager][Azure Resource Manager Overview] и [Использование тегов для организации ресурсов в Azure][Using Tags to organize your Azure Resources].
* Сведения о том, как теги могут помочь в управлении использованием ресурсов Azure, см. в статьях [Расшифровка счета за использование Microsoft Azure][Understanding your Azure Bill] и [Получение ценных сведений о потреблении ресурсов Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/management/manage-resources-cli.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]: ../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../cost-management-billing/manage/usage-rate-card-overview.md
