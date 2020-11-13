---
title: Пометка виртуальной машины Azure с помощью интерфейса командной строки
description: Сведения о добавлении тегов виртуальной машины с помощью Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 48f906bf0025bda03df226f32db1a0d6afdb9cee
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595071"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>Пометка виртуальной машины с помощью интерфейса командной строки

В этой статье описывается, как пометить виртуальную машину с помощью Azure CLI. Теги — это определяемые пользователем пары "ключ-значение", которые можно помещать непосредственно в ресурс или группу ресурсов. В настоящее время Azure поддерживает до 50 тегов на ресурс и группу ресурсов. Теги можно добавлять к ресурсу во время его создания или к уже существующему ресурсу. Вы также можете пометить виртуальную машину с помощью Azure [PowerShell](tag-powershell.md).


Можно просмотреть все свойства для данной виртуальной машины, включая теги, с помощью `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Чтобы добавить новый тег виртуальной машины с помощью Azure CLI, можно использовать `azure vm update` команду вместе с параметром Tag `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Чтобы удалить теги, можно использовать `--remove` параметр в `azure vm update` команде.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Теперь, когда мы применили теги к ресурсам с помощью Azure CLI и портала, рассмотрим сведения об использовании, чтобы увидеть теги на портале выставления счетов.


**Дальнейшие действия**

- Дополнительные сведения о добавлении тегов для ресурсов Azure см. в статьях [Общие сведения об Azure Resource Manager](../azure-resource-manager/management/overview.md) и [Использование тегов для организации ресурсов в Azure](../azure-resource-manager/management/tag-resources.md).
- Сведения о том, как теги могут помочь в управлении использованием ресурсов Azure, см. в статьях [Расшифровка счета за использование Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md) и [Получение ценных сведений о потреблении ресурсов Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
