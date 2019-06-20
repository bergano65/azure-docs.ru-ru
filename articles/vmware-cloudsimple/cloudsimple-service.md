---
title: Решение VMware службой CloudSimple
description: В этой статье приведены CloudSimple службы и основные понятия.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6a4c0bc6070d372a279b74f81ac1f84f565559c3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165242"
---
# <a name="cloudsimple-service-overview"></a>Общие сведения о службе CloudSimple

В службе CloudSimple можно использовать решение Azure VMware, CloudSimple. После создания службы, можно подготовить узлы, резерв узлы и создание частных облаков. Создайте службу CloudSimple в каждом регионе Azure, где доступна служба CloudSimple. Служба определяет сети периметра Azure VMware решения по CloudSimple. Пограничной сети поддерживает службы, которые включают VPN, Azure ExpressRoute и подключения к Интернету для ваших частных облаков.

## <a name="gateway-subnet"></a>Подсеть шлюза

Подсеть шлюза является обязательным согласно CloudSimple службы и является уникальным для региона, в котором она создается. Подсеть шлюза используется при создании сети периметра и требует/28 блок CIDR. Адресное пространство подсети шлюза должно быть уникальным. Он не должен пересекаться с какой-либо сети, который обменивается данными со средой CloudSimple. Сетей, которые взаимодействуют с CloudSimple включают локальными сетями и виртуальными сетями Azure. Невозможно удалить подсеть шлюза, после его создания. Подсеть шлюза удаляется при удалении службы.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [Создание CloudSimple службы в Azure](quickstart-create-cloudsimple-service.md).
