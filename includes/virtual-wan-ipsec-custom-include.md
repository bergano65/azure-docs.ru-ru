---
title: включить файл
description: включить файл
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72168395"
---
При работе с пользовательскими политиками IPsec имейте в виду следующие требования:

* **IKE** - Для IKE вы можете выбрать любой параметр из IKE Encryption, а также любой параметр от IKE Integrity, а также любой параметр из DH Group.
* **IPsec** - Для IPsec можно выбрать любой параметр из IPsec Encryption, а также любой параметр от IPsec Integrity, а также PFS. Если какой-либо из параметров для шифрования IPsec или Целостности IPsec является GCM, то параметры для обоих настроек должны быть GCM.

>[!NOTE]
> С политикой Custom IPsec отсутствует концепция ответчика и инициатора (в отличие от политики IPsec по умолчанию). Обе стороны (на территории и Azure VPN шлюз) будут использовать те же настройки для IKE Фаза 1 и IKE Фаза 2. Поддерживаются протоколы IKEv1 и IKEv2. Поддержка Azure только в качестве ответчика отсутствует.
>

**Доступные настройки и параметры**

| Параметр | Параметры |
|--- |--- |
| Шифрование IKE | AES256, AES192, AES128 |
| Целостность IKE | SHA384, SHA256, SHA1 |
| Группа DH | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| Шифрование IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| Целостность IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| Группа PFS | PFS24, ECP384, ECP256, PFS2048, PFS2 |
