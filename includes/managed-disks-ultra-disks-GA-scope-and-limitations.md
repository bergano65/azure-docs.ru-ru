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
Сейчас у Ultra Disks есть дополнительные ограничения:

- Поддерживаются в следующих регионах с различным количеством зон доступности на регион:
    - Восток США 2
    - Восточная часть США
    - Западный регион США 2
    - Юго-Восточная Азия
    - Северная Европа
    - Западная Европа
    - Южная часть Великобритании 
- Можно использовать только с Зонами доступности (для групп доступности и развертываний одной виртуальной машины за пределами зон невозможно подключить диск SSD (цен. категория "Ультра")).
- Поддерживаются только в следующей серии виртуальных машин:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - Серия fsv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Не каждый размер виртуальной машины доступен в каждом поддерживаемом регионе с Ultra Disks.
- Доступны только как диски данных и поддерживают только физические секторы размером 4 КБ.  
- Могут создаваться только как пустые диски.  
- Пока не поддерживаются моментальные снимки дисков, образы виртуальных машин, группы доступности и шифрование дисков Azure.
- Пока не поддерживают интеграцию с Azure Backup или Azure Site Recovery
- Текущее максимальное ограничение для операций ввода-вывода в общедоступных виртуальных машинах — 80 000.
- Если вы хотите принять участие в ограниченном предварительном просмотре виртуальной машины, которая может выполнять 160 000 операций ввода-вывода с помощью Ultra Disks, отправьте сообщение электронной почты UltraDiskFeedback@microsoft. com