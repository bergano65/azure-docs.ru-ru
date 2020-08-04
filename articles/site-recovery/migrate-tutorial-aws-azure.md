---
title: Перенос виртуальных машин AWS в Azure с помощью службы Azure Site Recovery | Документация Майкрософт
description: В этой статье описано, как перенести виртуальные машины Windows, запущенные в Amazon Web Services (AWS), в Azure с помощью Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dd91e99b45405cca10b9ddc2982674e72ad6bf86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281299"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Перенос виртуальных машин Amazon Web Services (AWS) в Azure

В этой статье описаны параметры для миграции экземпляров Amazon Web Services (AWS).

## <a name="migrate-with-azure-migrate"></a>Миграция с помощью средства "Миграция Azure"

Мы рекомендуем перенести экземпляры AWS в Azure с помощью службы [Миграция Azure](../migrate/migrate-services-overview.md). Средство "Миграция Azure" предоставляет централизованный узел для оценки и миграции локальных компьютеров в Azure, использования службы "Миграция Azure", других служб Azure и средств сторонних производителей.

[Узнайте](../migrate/tutorial-migrate-aws-virtual-machines.md) о миграции экземпляров AWS с помощью Миграции Azure. 


## <a name="migrate-with-site-recovery"></a>Миграция с помощью Site Recovery

Site Recovery следует использовать только для аварийного восстановления, а не для миграции.

Если вы уже используете Azure Site Recovery и хотите продолжить его использование для миграции AWS, выполните те же действия, что и при настройке [аварийного восстановления физических компьютеров](physical-azure-disaster-recovery.md).


> [!NOTE]
> При выполнении отработки отказа для аварийного восстановления в качестве последнего шага вы примените отработку отказа. При миграции экземпляров AWS параметр **Исполнить** не учитывается. Вместо этого следует выбрать параметр **Завершение миграции**. 

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Ознакомьтесь с часто задаваемыми вопросами о средстве "Миграция Azure"](../migrate/resources-faq.md).
