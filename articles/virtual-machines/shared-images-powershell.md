---
title: Создание коллекции общих образов с помощью Azure PowerShell
description: Узнайте, как использовать Azure PowerShell для создания общей коллекции образов в Azure
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: b7625903c4640fdb17eacd29822fe6203e075826
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796997"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Создание коллекции общих образов с помощью Azure PowerShell 

[Коллекция общих образов](./windows/shared-image-galleries.md) упрощает обмен пользовательскими образами в вашей организации. Пользовательские образы похожи на образы магазина, однако их можно создавать самостоятельно. Пользовательские образы можно использовать для начальной загрузки задач развертывания, например при предварительной загрузке приложений, конфигураций приложений и других конфигураций операционной системы. 

Коллекция общих образов позволяет предоставлять общий доступ к пользовательским образам виртуальной машины другим пользователям вашей организации в пределах региона или между регионами в клиенте AAD. Выберите образы, к которым нужно предоставить общий доступ, регионы, где они будут доступны, и пользователей, которым будет доступно совместное использование. Вы можете создать несколько коллекций, чтобы можно было логически группировать общие образы. 

Коллекция — это ресурс верхнего уровня, который обеспечивает полное управление доступом на основе ролей (RBAC). Образы могут быть версионными, и вы можете копировать каждую версию образа в другой набор регионов Azure. Коллекция работает только с Управляемыми образами.

Функция коллекции общих образов имеет несколько типов ресурсов. 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


[!INCLUDE [virtual-machines-common-shared-images-powershell](../../includes/virtual-machines-common-shared-images-powershell.md)]


## <a name="next-steps"></a>Дальнейшие действия

Создание образа из [виртуальной машины](image-version-vm-powershell.md), [управляемого образа](image-version-managed-image-powershell.md)или [образа в другой коллекции](image-version-another-gallery-powershell.md).

С помощью [построителя образов Azure (Предварительная версия)](./windows/image-builder-overview.md) можно автоматизировать создание версий изображений, а также использовать его для обновления и [создания новой версии образа из существующей версии образа](./windows/image-builder-gallery-update-image-version.md). 

Вы также можете создать ресурс коллекции общих образов с помощью шаблонов. Существует несколько шаблонов быстрого запуска Azure: 

- [Создание Общей коллекции образов.](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Создание определения образа в Общей коллекции образов](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Создание версии образа в Общей коллекции образов](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Создание виртуальной машины из версии образа](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


