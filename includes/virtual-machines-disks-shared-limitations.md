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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471716"
---
В ходе предварительного просмотра управляемые диски с включенными дисками подпадают под следующие ограничения:

- В настоящее время доступны только с премиум SSDs.
- В настоящее время поддерживается только в регионе Западно-Центральный США.
- Все виртуальные машины, совместно наделяющие диск, должны быть развернуты в одних и тех же [группах размещения.](../articles/virtual-machines/windows/proximity-placement-groups.md)
- Может быть включентолько только на дисках данных, а не на дисках ОС.
- Только базовые диски могут быть использованы с некоторыми версиями Windows Server Failover кластера, для деталей увидеть [Failover кластеризации аппаратных требований и вариантов хранения.](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)
- ReadOnly кэширования хоста не доступен `maxShares>1`для премиальных SSD с .
- Наборы доступности и виртуальные наборы `FaultDomainCount` масштабов машин могут быть использованы только с набором до 1.
- Поддержка резервного копирования Azure и восстановления сайта Azure пока недоступна.

Если вы заинтересованы в попытке общих дисков, то [подпишитесь на наш предварительный просмотр.](https://aka.ms/AzureSharedDiskPreviewSignUp)
