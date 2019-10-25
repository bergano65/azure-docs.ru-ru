---
title: Требования к серверу конфигурации для аварийного восстановления VMware в Azure с помощью Azure Site Recovery | Документация Майкрософт
description: В этой статье описываются требования и поддержка при развертывании сервера конфигурации для аварийного восстановления VMware в Azure с помощью Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: d83b99ea540d6232f4c0786d3a743f97332e1c9f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792318"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Требования к серверу конфигурации для аварийного восстановления VMware в Azure

При использовании [Azure Site Recovery](site-recovery-overview.md) для аварийного восстановления виртуальных машин VMware и физических серверов в Azure развертывается локальный сервер конфигурации.

- Сервер конфигурации используется для управления обменом данными между локальной средой VMware и Azure. Он также управляет репликацией данных.
- [Подробнее](vmware-azure-architecture.md) о компонентах и процессах сервера конфигурации.

## <a name="configuration-server-deployment"></a>Развертывание сервера конфигурации

Для аварийного восстановления виртуальных машин VMware в Azure вы развертываете сервер конфигурации как виртуальную машину VMware.

- Site Recovery предоставляет шаблон OVA, который нужно загрузить с портала Azure и импортировать в vCenter Server для настройки виртуальной машины сервера конфигурации.
- При развертывании сервера конфигурации с использованием шаблона OVA виртуальная машина автоматически соответствует требованиям, перечисленным в этой статье.
- Мы настоятельно рекомендуем настроить сервер конфигурации с помощью шаблона OVA. Однако, если вы настраиваете аварийное восстановление для виртуальных машин VMware и не можете использовать шаблон OVA, можно развернуть сервер конфигурации, следуя [этим инструкциям](physical-azure-set-up-source.md).
- При развертывании сервера конфигурации для аварийного восстановления локальных физических компьютеров в Azure следуйте инструкциям в [этой статье](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Дальнейшие действия
Настройка аварийного восстановления [виртуальных машин VMware](vmware-azure-tutorial.md) в Azure.
