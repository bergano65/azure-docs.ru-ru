---
title: включить файл
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935824"
---
На данный момент, ультра диски имеют дополнительные ограничения, они являются следующими:

- Поддерживаются в следующих регионах с разным числом зон доступности в регионе:
    - восточная часть США 2
    - Восточная часть США
    - западная часть США 2
    - юго-восточная Азия
    - Северная Европа
    - Западная Европа
    - южная часть Соединенного Королевства 
- Можно использовать только с Зонами доступности (для групп доступности и развертываний одной виртуальной машины за пределами зон невозможно подключить диск SSD (цен. категория "Ультра")).
- Поддерживаются только в следующих сериях VM:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [М](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Не каждый размер VM доступен в каждом поддерживаемом регионе с ультра-дисками
- Доступны только в качестве дисков данных и поддерживают только размер физического сектора 4k. Из-за 4K родной размер сектора Ultra Disk, Есть некоторые приложения, которые не будут совместимы с ультра дисков. Одним из примеров может быть Oracle Database, которая требует выпуска 12.2 или позже для поддержки ультра дисков.  
- Могут создаваться только как пустые диски.  
- Пока не поддерживаете снимки диска, Изображения VM, наборы доступности и шифрование дисков Azure
- Еще не поддерживаете интеграцию с резервной поддержкой Azure или восстановлением сайта Azure
- Текущий максимальный лимит для IOPS на ГВМ составляет 80 000.
- Если вы хотите принять участие в ограниченном просмотре VM, который может выполнить 160000 IOPS с ультра дисков, пожалуйста, напишите UltraDiskFeedback@microsoft .com