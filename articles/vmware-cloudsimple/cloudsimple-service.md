---
title: Решение Azure VMware от CloudSimple - Сервис
description: Предоставляет обзор сервиса и концепций CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024963"
---
# <a name="cloudsimple-service-overview"></a>Обзор сервиса CloudSimple

Сервис CloudSimple позволяет использовать решение Azure VMware от CloudSimple.  Создание сервиса позволяет приобрести узлы, зарезервировать узлы и создать private Clouds.  Вы создаете службу CloudSimple в каждом регионе Azure, где доступна служба CloudSimple. Служба определяет край сети решений Azure VMware по CloudSimple. Сеть edge поддерживает услуги, которые включают VPN, ExpressRoute и подключение к Интернету в ваших частных облаках.

## <a name="gateway-subnet"></a>Подсеть шлюза

Подсеть шлюза необходима для службы CloudSimple и уникальна для региона, в котором она создана. Подсеть шлюза используется при создании краевой сети и требует блока CIDR /28.  Пространство адреса шлюза должно быть уникальным. Он не должен пересекаться с любой сетью, которая общается с средой CloudSimple. Сети, взаимодействуют с CloudSimple, включают в себя сети и виртуальную сеть Azure.  Подсеть шлюза не может быть удалена после ее создания.  Подсеть шлюза удаляется при удалении службы.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [создать сервис CloudSimple в Azure.](quickstart-create-cloudsimple-service.md)
