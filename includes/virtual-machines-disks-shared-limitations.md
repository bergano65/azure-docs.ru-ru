---
title: включить файл
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471716"
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

Если вы заинтересованы в попытке использовать общие диски, [Зарегистрируйтесь для использования предварительной версии](https://aka.ms/AzureSharedDiskPreviewSignUp).
