---
title: включение файла
description: включение файла
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168395"
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
| Шифрование IKE | AES256, AES192, AES128 |
| Целостность IKE | SHA384, SHA256, SHA1 |
| Группа DH | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| Шифрование IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| Целостность IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| Группа PFS | PFS24, ECP384, ECP256, PFS2048, PFS2 |
