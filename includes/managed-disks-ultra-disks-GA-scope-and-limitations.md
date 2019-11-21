---
title: включение файла
description: включение файла
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: df488c2a3033015175943f7d092beaaa41e34046
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260858"
---
For now, ultra disks have additional limitations, they are as follows:

- Are supported in the following regions, with a varying number of availability zones per region:
    - Восточная часть США 2
    - Восточная часть США
    - Западная часть США 2
    - SouthEast Asia
    - Северная Европа
    - Западная Европа
    - Южная часть Соединенного Королевства 
- Можно использовать только с Зонами доступности (для групп доступности и развертываний одной виртуальной машины за пределами зон невозможно подключить диск SSD (цен. категория "Ультра")).
- Are only supported on the following VM series:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Not every VM size is available in every supported region with ultra disks.
- Доступны только как диски данных и поддерживают только физические секторы размером 4 КБ.  
- Могут создаваться только как пустые диски.  
- Do not yet support disk snapshots, VM images, availability sets, and Azure disk encryption
- Do not yet support integration with Azure Backup or Azure Site Recovery
- The current maximum limit for IOPS on GA VMs is 80,000.
- If you would like to participate in a limited preview of a VM that can accomplish 160,000 IOPS with ultra disks, please email UltraDiskFeedback@microsoft .com