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
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935824"
---
Сейчас у Ultra Disks есть дополнительные ограничения:

- Поддерживаются в следующих регионах с различным количеством зон доступности на регион:
    - Восточная часть США 2
    - Восточная часть США
    - Западная часть США 2
    - Юго-Восточная Азия
    - Северная Европа
    - Западная Европа
    - Южная часть Соединенного Королевства 
- Можно использовать только с Зонами доступности (для групп доступности и развертываний одной виртуальной машины за пределами зон невозможно подключить диск SSD (цен. категория "Ультра")).
- Поддерживаются только в следующей серии виртуальных машин:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - Серия fsv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Не каждый размер виртуальной машины доступен в каждом поддерживаемом регионе с Ultra Disks.
- Доступны только в виде дисков данных и поддерживают только объем физического сектора 4 КБ. Из-за объема собственного сектора в 4 КБ некоторые приложения не будут совместимы с Ultra Disks. Примером может быть Oracle Database, для поддержки которых требуется выпуск 12,2 или более поздней версии.  
- Могут создаваться только как пустые диски.  
- Пока не поддерживаются моментальные снимки дисков, образы виртуальных машин, группы доступности и шифрование дисков Azure.
- Пока не поддерживают интеграцию с Azure Backup или Azure Site Recovery
- Текущее максимальное ограничение для операций ввода-вывода в общедоступных виртуальных машинах — 80 000.
- Если вы хотите принять участие в ограниченном предварительном просмотре виртуальной машины, которая может выполнять 160 000 операций ввода-вывода с помощью Ultra Disks, отправьте сообщение электронной почты UltraDiskFeedback@microsoft. com