---
title: Включить имя файла
description: включить файл
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e2a950037aed2a8ded4d4e55920721285cbfc05c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204475"
---
При работе с пользовательскими политиками IPsec учитывайте следующие требования.

* **IKE** — для IKE можно выбрать любой параметр из шифрования IKE, а также любой параметр из группы Диффи-Хелмана.
* **IPSec** — для IPSec можно выбрать любой параметр из шифрования IPSec, а также любой параметр из целостности IPSec, а также PFS. Если какой-либо из параметров для шифрования IPsec или IPsec имеет значение GCM, то параметры обоих параметров должны быть GCM.

>[!NOTE]
> В пользовательских политиках IPsec нет концепции ответчика и инициатора (в отличие от политик IPsec по умолчанию). Обе стороны (локальный и VPN-шлюз Azure) будут использовать те же параметры, что и на этапе IKE 1 и на этапе 2 IKE. Поддерживаются протоколы IKEv1 и IKEv2. Azure не поддерживается только в качестве респондента.
>

**Доступные параметры и параметры**

| Параметр | Параметры |
|--- |--- |
| Шифрование IKE | GCMAES256, GCMAES128, AES256, AES128 |
| Целостность IKE | SHA384, SHA256 |
| Группа DH | ECP384, ECP256, DHGroup24, DHGroup14 |
| Шифрование IPsec | GCMAES256, GCMAES128, AES256, AES128, нет |
| Целостность IPsec | GCMAES256, GCMAES128, SHA256 |
| Группа PFS | ECP384, ECP256, PFS24, PFS14, нет |
