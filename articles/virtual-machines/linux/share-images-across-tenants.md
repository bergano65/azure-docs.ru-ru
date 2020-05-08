---
title: Совместное использование образов коллекций в клиентах в Azure
description: Узнайте, как совместно использовать образы виртуальных машин в клиентах Azure с помощью общих коллекций образов.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 62f136eb1c2684bfd307a05ccbb4836739da6f3a
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789459"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Совместное использование образов виртуальных машин коллекции в клиентах Azure

Коллекции общих образов позволяют обмениваться изображениями с помощью RBAC. RBAC можно использовать для совместного использования образов в клиенте и даже для пользователей за пределами клиента. Дополнительные сведения об этом простом параметре общего доступа см. в разделе [общий доступ к коллекции](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Вы не можете использовать портал для развертывания виртуальной машины из образа в другом клиенте Azure. Чтобы создать виртуальную машину из образа, совместно используемого клиентами, необходимо использовать Azure CLI или [PowerShell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Создание виртуальной машины с помощью Azure CLI

Войдите в субъект-службу для клиента 1, используя appID, ключ приложения и идентификатор клиента 1. При необходимости можно `az account show --query "tenantId"` использовать для получения идентификаторов клиентов.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Войдите в субъект-службу для клиента 2, используя appID, ключ приложения и идентификатор клиента 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Создайте виртуальную машину. Замените сведения в примере собственными.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы столкнетесь с проблемами, обратитесь к статье об [устранении неполадок с коллекциями общих образов](troubleshooting-shared-images.md).