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
ms.openlocfilehash: d480d880d9b26666f393c179efa953b653c80c3e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881997"
---
# <a name="list-update-and-delete-image-resources-using-powershell"></a>Перечисление, обновление и удаление ресурсов изображений с помощью PowerShell 

Вы можете управлять ресурсами коллекции общих образов с помощью Azure PowerShell.

[!INCLUDE [virtual-machines-common-gallery-list-ps.md](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>Дальнейшие действия

С помощью [построителя образов Azure (Предварительная версия)](./image-builder-overview.md) можно автоматизировать создание версий изображений, а также использовать его для обновления и [создания новой версии образа из существующей версии образа](./linux/image-builder-gallery-update-image-version.md).