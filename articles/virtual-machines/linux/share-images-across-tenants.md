---
title: Совместное изображение галереи между арендаторами в Azure
description: Узнайте, как обмениваться изображениями VM между арендаторами Azure с помощью общих галерей изображений.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 18337620a6f9506e402149909667026e4a8ba7eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034971"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Поделитесь изображениями галереи VM между арендаторами Azure

Общие изображения галереи позволяют обмениваться изображениями с помощью RBAC. Вы можете использовать RBAC для обмена изображениями в вашем арендаторе, и даже для физических лиц за пределами вашего арендатора. Для получения дополнительной информации об этом простом варианте [совместного](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery)использования см.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Вы не можете использовать портал для развертывания VM из изображения в другом лазурном арендаторе. Для создания VM из изображения, совместно гостем, совместно с арендаторами, необходимо использовать Azure CLI или [Powershell.](../windows/share-images-across-tenants.md)

## <a name="create-a-vm-using-azure-cli"></a>Создание VM с помощью Azure CLI

Войти в основной сервис для арендатора 1 с помощью appID, ключа приложения и идентификатора арендатора 1. Вы можете `az account show --query "tenantId"` использовать, чтобы получить итоговые иудки арендатора, если это необходимо.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Войти в основной сервис для арендатора 2 с помощью appID, ключа приложения и идентификатора арендатора 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Создайте виртуальную машину. Замените информацию в примере своей.

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