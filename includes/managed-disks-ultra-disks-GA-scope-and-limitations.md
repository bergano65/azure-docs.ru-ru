---
title: включение файла
description: включение файла
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c847b110cb821ebe3ca7e706fcaec592dcc58fa8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600972"
---
Сейчас у Ultra Disks есть дополнительные ограничения:

- Поддерживаются в следующих регионах с различным количеством зон доступности на регион:
    - Восток США 2
    - Восток США
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