---
title: Как перенести управление API Azure в разных регионах
description: Узнайте, как перенести экземпляр управления API из одного региона в другой.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 3294a7b2112e9527041ef343f4452aedb7a2a272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073468"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Как перенести управление API Azure в разных регионах
Для переноса экземпляров управления API из одного региона Azure в другой можно использовать [функцию резервного копирования и восстановить.](api-management-howto-disaster-recovery-backup-restore.md) Вы должны выбрать один и тот же уровень ценообразования API Management в исходных и целевых регионах. 

> [!NOTE]
> Резервное копирование и восстановление не будут работать во время миграции между различными типами облаков. Для этого вам нужно будет экспортировать ресурс [в качестве шаблона.](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates) Затем адаптируйте экспортированный шаблон для целевого региона Azure и воссоздайте ресурс. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Вариант 1: Используйте другое имя экземпляра Управления API

1. В целевом регионе создайте новый экземпляр Управления API с тем же уровнем ценообразования, что и экземпляр управления API.aPI. Новый экземпляр должен иметь другое имя. 
1. Резервное копирование существующего экземпляра Управления API на учетную запись хранилища.
1. Восстановление резервного копирования, созданного в шаге 2, в новый экземпляр управления API, созданный в новом регионе в шаге 1.
1. Если у вас есть пользовательский домен, указывающий на экземпляр API Management региона исходного кода, обновите пользовательский домен CNAME, чтобы указать на новый экземпляр Управления API. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Вариант 2: Используйте одно и то же имя экземпляра Управления API

> [!NOTE]
> Эта опция приведет к простою во время миграции.

1. Резервное копирование экземпляра управления API в исходном регионе в учетную запись хранилища.
1. Удалите управление API в регионе исходного кода. 
1. Создайте новый экземпляр управления API в целевом регионе с тем же названием, что и в регионе исходного кода.
1. Восстановление резервного копирования, созданного в шаге 1, в новый экземпляр управления API в целевом регионе.  


## <a name="next-steps"></a><a name="next-steps"> </a>Дальнейшие действия
* Для получения дополнительной информации о функции резервного копирования и восстановления [см.](api-management-howto-disaster-recovery-backup-restore.md)
* Для получения информации о [Azure cross-region migration guidance](https://github.com/Azure/Azure-Migration-Guidance)ресурсах миграции Azure см.