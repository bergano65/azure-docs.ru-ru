---
title: Решения Azure VMware (AVS) — служба
description: Содержит общие сведения о службе и концепциях AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024963"
---
# <a name="avs-service-overview"></a>Общие сведения о службе AVS

Служба AVS позволяет использовать решение VMware для Azure с помощью AVS. Создание службы позволяет покупать узлы, резервировать узлы и создавать частные облака AVS. Вы создадите службу AVS в каждом регионе Azure, где доступна служба AVS. Служба определяет пограничной сеть решения VMware для Azure с помощью AVS. Пограничная сеть поддерживает службы, которые включают VPN, ExpressRoute и подключение к Интернету для частных облаков AVS.

## <a name="gateway-subnet"></a>Подсеть шлюза

Для каждой службы AVS требуется подсеть шлюза, которая является уникальной для региона, в котором она создана. Подсеть шлюза используется при создании пограничной сети и требует блока/28 CIDR. Адресное пространство подсети шлюза должно быть уникальным. Она не должна пересекаться с сетью, которая взаимодействует с средой AVS. Сети, взаимодействующие с AVS, включают в себя локальные сети и виртуальную сеть Azure. Подсеть шлюза невозможно удалить после ее создания. Подсеть шлюза удаляется при удалении службы.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [создать службу AVS в Azure](quickstart-create-cloudsimple-service.md).
