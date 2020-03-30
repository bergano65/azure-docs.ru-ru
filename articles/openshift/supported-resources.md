---
title: Поддерживаемые ресурсы для Azure Red Hat OpenShift
description: Поймите, какие регионы Azure и виртуальные размеры машин поддерживаются Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243670"
---
# <a name="azure-red-hat-openshift-resources"></a>Ресурсы Azure Red Hat OpenShift

В этой теме перечислены регионы Azure и размеры виртуальных машин, поддерживаемые службой Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Регионы Azure

Просрняйте [продукты, доступные по регионам,](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) для текущего списка регионов, где можно развернуть кластеры Azure Red Hat OpenShift.

## <a name="virtual-machine-sizes"></a>Размер виртуальных машин

Ниже приведены поддерживаемые размеры виртуальных машин, которые можно указать для вычислительных узлов в кластере Azure Red Hat OpenShift.

> [!Important]
> Каждый VM имеет различное количество дисков, которые могут быть прикреплены. Это может быть не так сразу ясно, как память или размер процессора.
> Не во всех регионах доступны виртуальные машины всех размеров. Даже если API поддерживает указанный размер, можно получить ошибку, если размер недоступен в указанном регионе.
> Для получения дополнительной информации смотрите [текущий список поддерживаемых размеров VM в регионе.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)

## <a name="compute-node-sizes"></a>Размеры вычислительных узлов

Следующие размеры вычислительных узлов поддерживаются API Azure Red Hat OpenShift REST:

|Размер|vCPU|ОЗУ|
|-|-|-|
|Стандартный D4s v3|4|16 ГБ|
|Стандартный D8s v3|8|32 ГБ|
|Стандартный D16s v3|16|64 ГБ|
|Стандартный D32s v3|32|128 ГБ|
|-|-|-|
|Стандартный E4s v3|4|32 ГБ|
|Стандартные E8s v3|8|64 ГБ|
|Стандартный E16s v3|16|128 ГБ|
|Стандартный E32s v3|32|256 ГБ|
|-|-|-|
|Стандартные F8s v2|8|16 ГБ|
|Стандартные F16s v2|16|32 ГБ|
|Стандартные F32s v2|32|64 ГБ|

## <a name="master-node-sizes"></a>Размеры магистратных узлов

Следующие размеры узлов мастер/инфраструктуры поддерживаются API Azure Red Hat OpenShift REST:

|Размер|vCPU|ОЗУ|
|-|-|-|
|Стандартный D4s v3|4|16 ГБ|
|Стандартный D8s v3|8|32 ГБ|
|Стандартный D16s v3|16|64 ГБ|
|Стандартный D32s v3|32|128 ГБ|

## <a name="next-steps"></a>Дальнейшие действия

Попробуйте [создать учебник кластера Azure Red Hat OpenShift.](tutorial-create-cluster.md)
