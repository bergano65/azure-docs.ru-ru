---
title: Решения для VMware от CloudSimple - службы
description: В этой статье приведены CloudSimple службы и основные понятия.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f7b4be0ff3997e27dd5b5321dd44b5006ae52102
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358113"
---
# <a name="cloudsimple-service-overview"></a>Общие сведения о службе CloudSimple

Служба CloudSimple позволяет использовать решения Azure VMware с CloudSimple.  Создание службы позволяет приобрести узлов, Резервировать узлов и создание частных облаков.  Создайте службу CloudSimple в каждом регионе Azure, где доступна служба CloudSimple.  Служба определяет сети периметра Azure VMware решения по CloudSimple.  Пограничной сети поддерживает службы, которые включают VPN, ExpressRoute и подключения к Интернету, чтобы ваши Частные облака.

## <a name="gateway-subnet"></a>Подсеть шлюза

Подсеть шлюза является обязательным согласно CloudSimple службы и является уникальным для региона, в котором она создается. Подсеть шлюза используется при создании сети периметра и требует/28 блок CIDR.  Адресное пространство подсети шлюза должно быть уникальным. Он не должен пересекаться с какой-либо сети, который обменивается данными со средой CloudSimple. Сетей, которые взаимодействуют с CloudSimple включают для локальных сетей и виртуальной сети Azure.  Невозможно удалить подсеть шлюза, после ее создания.  Подсеть шлюза удаляется при удалении службы.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [Создание CloudSimple службы в Azure](quickstart-create-cloudsimple-service.md)