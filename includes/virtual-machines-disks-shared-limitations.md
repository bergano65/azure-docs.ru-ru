---
title: включить файл
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4c150d874d56e3de495b0682bee979d13304a01d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202220"
---
В предварительной версии управляемые диски с включенными общими дисками подвергаются следующим ограничениям.

- Сейчас доступно только для твердотельных накопителей уровня "Премиум".
- В настоящее время поддерживается только в регионе "Западная Центральная часть США".
- Все виртуальные машины, совместно использующие диск, должны быть развернуты в одних и тех же [группах размещения](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Можно включить только на дисках данных, а не на дисках ОС.
- В некоторых версиях отказоустойчивого кластера Windows Server можно использовать только базовые диски. Дополнительные сведения см. в разделе [требования к оборудованию отказоустойчивой кластеризации и параметры хранения](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Кэширование узлов с доступом только для чтения недоступно для служб SSDs уровня "Премиум" с `maxShares>1`.
- Группы доступности и масштабируемые наборы виртуальных машин можно использовать только с `FaultDomainCount`, для которых задано значение 1.
- Поддержка Azure Backup и Azure Site Recovery пока недоступна.

Если вы заинтересованы в попытке использовать общие диски, [Зарегистрируйтесь для использования предварительной версии](https://aka.ms/shareddisksignup).
