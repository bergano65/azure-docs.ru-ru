---
title: Перечисление, обновление и удаление ресурсов изображений с помощью PowerShell
description: Перечисление, обновление и удаление ресурсов изображений в коллекции общих образов с помощью Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/27/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c6bc4fea05f79c446f318eb4cbc86beb160ea108
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82797010"
---
# <a name="list-update-and-delete-image-resources-using-powershell"></a>Перечисление, обновление и удаление ресурсов изображений с помощью PowerShell 

Вы можете управлять ресурсами коллекции общих образов с помощью Azure PowerShell.

[!INCLUDE [virtual-machines-common-gallery-list-ps.md](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>Дальнейшие шаги

С помощью [построителя образов Azure (Предварительная версия)](./linux/image-builder-overview.md) можно автоматизировать создание версий изображений, а также использовать его для обновления и [создания новой версии образа из существующей версии образа](./linux/image-builder-gallery-update-image-version.md).